---
title: "Rust on the Flipper Zero — Jakob's Personal Webpage"
source: "https://jakob.space/blog/rust-on-flipper-zero.html"
author:
  - "[[Jakob L. Kreuze]]"
published: #<date nanosecond: 0 second: 54 minute: 24 hour: 11 day: 5 month: 7 year: 2022 zone-offset: -14400>
created: 2025-06-23
description: "My Flipper Zero arrived in the mail a few weeks ago, ending a nearly two-yearwait for its arrival. For the uninitiated, it's a \"multi-tool device for geeks\":a development board for radio, IR, and GPIO in a Tamagotchi -like form-factor. Itcombines the capability of the GoodWatch with the cuteness of the Pwnagotchi .Part of the appeal, to me, is the ability to hack on the free (as in freedom)firmware . As capable as it was out of the box, providing plenty of amusementwhen my brother and I took it for a spin through some parking garages, there arestill features I'd like to add to it. The problem is that I've been too pamperedby Rust as of late to want to do my firmware hacks in C."
tags:
  - "clippings"
  - "rust"
---
## Rust on the Flipper Zero

July 05, 2022 ❖ Tags: , , , ,

My [Flipper Zero](https://flipperzero.one/) arrived in the mail a few weeks ago, ending a nearly two-year wait for its arrival. For the uninitiated, it's a "multi-tool device for geeks": a development board for radio, IR, and GPIO in a [Tamagotchi](https://en.wikipedia.org/wiki/Tamagotchi) -like form-factor. It combines the capability of the [GoodWatch](https://kk4vcz.com/goodwatch/) with the cuteness of the [Pwnagotchi](https://pwnagotchi.ai/). Part of the appeal, to me, is the ability to hack on the [free (as in freedom) firmware](https://github.com/flipperdevices/flipperzero-firmware). As capable as it was out of the box, providing plenty of amusement when my brother and I took it for a spin through some parking garages, there are still features I'd like to add to it. The problem is that I've been too pampered by Rust as of late to want to do my firmware hacks in C.

Indeed, shortly after receiving the Flipper, I updated the firmware as directed. While mostly solid, I had a lock-up within the first hour of use, forcing me to give it the two-finger salute to reboot it. Obviously, these things inevitably happen. It's early in the product's lifecycle. And even though I trust the Flipper developers to wield the n-edged blade of C, I know that *I'm* not a firmware engineer by trade, and that I would want the comfort of the borrow checker to prevent the many more locks and freezes I would introduce if I were to write my changes in C.

Embedded software is a field where Rust (supposedly) [shines](https://artemis.sh/2022/03/28/oxide-hubris-on-pinetime.html),and I happened to come across Philipp Oppermann's book *Writing an OS in Rust* recently (specifically [this section](https://os.phil-opp.com/freestanding-rust-binary/)) which inspired me to give it a whirl on my new toy.

## Shoehorning Rust Into a C Build System

Our first goal will be to get "hello world" running on the Flipper. We'll do something actually useful with the Flipper API eventually, but we must walk before we can run.

The firmware build process is [well-documented](https://github.com/flipperdevices/flipperzero-firmware/blob/dev/ReadMe.md). It's primarily written in C and uses `make`. We have two feasible options for shoehorning Rust into the process. We can:

1. Use `rustc` to emit an [object file](https://stackoverflow.com/questions/37891559/rusts-o-file-format-not-recognized-by-gcc), which we can add to the list of objects being linked by build system.
2. Create a proper `cargo` project and produce a [static C library](https://en.wikipedia.org/wiki/Static_library#Creating_static_libraries_in_C/C++), which we'll then link into the firmware.

The latter has the benefit that we can easily use `no-std` libraries and inject custom logic into a `build.rs` (e.g. for generating bindings as part of the build process). But before settling on one particular solution, I wanted data on cost in terms of firmware size – one of several limitations we need to consider as we hack on the firmware.

### Linking an Object File

I cracked open a beer Saturday afternoon and sat down to get through the web of Makefiles, eventually converging on this diff to include a `rustc` rule:

```
diff --git a/applications/applications.c b/applications/applications.c
index 247c601c..67e70e8d 100644
--- a/applications/applications.c
+++ b/applications/applications.c
@@ -49,6 +49,7 @@ extern int32_t file_browser_app(void* p);
 // Plugins
 extern int32_t music_player_app(void* p);
 extern int32_t wav_player_app(void* p);
+extern int32_t hello_world_app(void *p);
 extern int32_t clock_app(void *p);
 extern int32_t unirfremix_app(void *p);
 extern int32_t spectrum_analyzer_app(void* p);
@@ -382,6 +383,14 @@ const FlipperApplication FLIPPER_PLUGINS[] = {
      .flags = FlipperApplicationFlagDefault},
 #endif

+#ifdef APP_HELLO_WORLD
+    {.app = hello_world_app,
+     .name = "Hello World",
+     .stack_size = 1024,
+     .icon = NULL,
+     .flags = FlipperApplicationFlagDefault},
+#endif
+
 };

 const size_t FLIPPER_PLUGINS_COUNT = COUNT_OF(FLIPPER_PLUGINS);
diff --git a/applications/applications.mk b/applications/applications.mk
index 60a339bd..baeaf4ae 100644
--- a/applications/applications.mk
+++ b/applications/applications.mk
@@ -4,6 +4,7 @@ LIB_DIR         = $(PROJECT_ROOT)/lib
 CFLAGS         += -I$(APP_DIR)
 C_SOURCES      += $(shell find $(APP_DIR) -name "*.c")
 CPP_SOURCES    += $(shell find $(APP_DIR) -name "*.cpp")
+RUST_SOURCES += $(shell find $(APP_DIR) -name "*.rs")

 RAM_EXEC ?= 0
 ifeq ($(RAM_EXEC), 1)
@@ -53,6 +54,7 @@ APP_SPECTRUM_ANALYZER = 1
 APP_MUSIC_PLAYER = 1
 APP_SNAKE_GAME = 1
 APP_WAV_PLAYER = 1
+APP_HELLO_WORLD = 1
 APP_TETRIS_GAME = 1

 # Debug
@@ -253,6 +255,12 @@ CFLAGS             += -DAPP_WAV_PLAYER
 SRV_GUI                = 1
 endif

+APP_HELLO_WORLD ?= 0
+ifeq ($(APP_HELLO_WORLD), 1)
+CFLAGS         += -DAPP_HELLO_WORLD
+SRV_GUI                = 1
+endif
+
 APP_TETRIS_GAME ?= 0
 ifeq ($(APP_TETRIS_GAME), 1)
 CFLAGS         += -DAPP_TETRIS_GAME
diff --git a/applications/hello_world/hello_world.rs b/applications/hello_world/hello_world.rs
new file mode 100644
index 00000000..93a0b1b8
--- /dev/null
+++ b/applications/hello_world/hello_world.rs
@@ -0,0 +1,15 @@
+#![no_std]
+#![crate_type = "staticlib"]
+
+use core::panic::PanicInfo;
+
+#[no_mangle]
+pub extern "C" fn hello_world_app() -> i32 {
+    0
+}
+
+/// This function is called on panic.
+#[panic_handler]
+fn panic(_info: &PanicInfo) -> ! {
+    loop {}
+}
diff --git a/make/base.mk b/make/base.mk
index 281419e9..ec277016 100644
--- a/make/base.mk
+++ b/make/base.mk
@@ -1,7 +1,8 @@
 OBJ_DIR                        = .obj
 ASM_SOURCES            = 
 C_SOURCES              = 
-CPP_SOURCES            = 
+CPP_SOURCES            =
+RUST_SOURCES   =
 ASSETS                 = 
 OPENOCD_OPTS   = 

diff --git a/make/rules.mk b/make/rules.mk
index bdf8a4af..68b62c0c 100644
--- a/make/rules.mk
+++ b/make/rules.mk
@@ -4,11 +4,13 @@ OBJ_DIR := $(OBJ_DIR)/$(TARGET)-$(PROJECT)
 C_SOURCES := $(abspath ${C_SOURCES})
 ASM_SOURCES := $(abspath ${ASM_SOURCES})
 CPP_SOURCES := $(abspath ${CPP_SOURCES})
+RUST_SOURCES := $(abspath ${RUST_SOURCES})

 # Gather object
 OBJECTS = $(addprefix $(OBJ_DIR)/, $(C_SOURCES:.c=.o))
 OBJECTS += $(addprefix $(OBJ_DIR)/, $(ASM_SOURCES:.s=.o))
 OBJECTS += $(addprefix $(OBJ_DIR)/, $(CPP_SOURCES:.cpp=.o))
+OBJECTS += $(addprefix $(OBJ_DIR)/, $(RUST_SOURCES:.rs=.o))

 OBJECT_DIRS = $(sort $(dir $(OBJECTS)))

@@ -78,6 +80,10 @@ $(OBJ_DIR)/%.o: %.cpp $(OBJ_DIR)/BUILD_FLAGS
        @echo "\tCPP\t" $(subst $(PROJECT_ROOT)/, , $<)
        @$(CPP) $(CFLAGS) $(CPPFLAGS) -c $< -o $@

+$(OBJ_DIR)/%.o: %.rs $(OBJ_DIR)/BUILD_FLAGS
+       @echo "\tRUSTC\t" $(subst $(PROJECT_ROOT)/, , $<)
+       @$(RUSTC) $< -o $@
+
 $(OBJ_DIR)/flash: $(OBJ_DIR)/$(PROJECT).bin
        openocd $(OPENOCD_OPTS) -c "program $(OBJ_DIR)/$(PROJECT).bin reset exit $(FLASH_ADDRESS)" 
        touch $@
diff --git a/make/toolchain.mk b/make/toolchain.mk
index f161d3d1..9a27ca8d 100644
--- a/make/toolchain.mk
+++ b/make/toolchain.mk
@@ -12,6 +12,7 @@ CCACHE := $(shell which ccache)

 CC     = $(CCACHE) $(PREFIX)gcc -std=gnu17
 CPP    = $(CCACHE) $(PREFIX)g++ -std=gnu++17
+RUSTC = rustc --target thumbv7em-none-eabihf --emit obj
 LD     = $(PREFIX)g++
 AS     = $(PREFIX)gcc -x assembler-with-cpp
 CP     = $(PREFIX)objcopy
```

I took my usual approach of copy/pasting the surrounding code and letting whatever errors the build system spits out guide my changes. The mortal sin present above is a hard-coded processor architecture. Also, note that this is `thumbv7em-none-eabihf` and not `thumbv7em-none-eabi`, which I used in the beginning to little success. The Flipper Zero has a STM32WB55RG MCU. Rust's "Embedded Rust Book" uses a similar STM32F3DISCOVERY, but the Rust targets are different. Using the latter target will net you an error:

Anyway, here's the `hello_world.rs` with better syntax highlighting than what `diff-mode` gives us:

```
#![no_std]
#![crate_type = "staticlib"]

use core::panic::PanicInfo;

#[no_mangle]
pub extern "C" fn hello_world_app() -> i32 {
    0
}

/// This function is called on panic.
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```

This should read as Rust, more-or-less. We specify `#![no_std]`, because we won't have `std` available in an embedded environment. We also specify `#![crate_type = "staticlib"]` because we're targeting a C object file and don't want an error about an absent `main` function. We're exporting a function that's going to be called by some C code outside `rustc` 's purview, so we specify `#[no_mangle]` and `extern "C"` – that much should be reasonably familiar to anyone who has used Rust's FFI interface. Finally, in `no_std` land, we don't get to assume that there's a nice `panic` handler there for us. We have to implement that ourselves. We don't have a great way of handling it at this point, so we'll just loop indefinitely.

But before we compile with those changes, we'll need a baseline size. Building `9d46f62be1ffa17bc8d13b68b8708ccfec17e82e` on Gentoo with `gcc-arm-none-eabi-10.3-2021.10`:

```
...
\tLD\t .obj/f7-firmware/firmware.elf
   text    data     bss     dec     hex filename
 883808    1696  362723 1248227  130be3 .obj/f7-firmware/firmware.elf

jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
16361148        dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
885821  dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
```

Note that `dist/` contains a couple of files – `firmware.elf` is the actual linker output, but `.dfu` is the output in the format that's flashed to the Flipper. Now, we can confirm that our changes to the build system work as desired if we run it without adding `applications/hello_world`:

```
...
\tLD\t .obj/f7-firmware/firmware.elf
/opt/gcc-arm-none-eabi-10.3-2021.10/bin/../lib/gcc/arm-none-eabi/10.3.1/../../../../arm-none-eabi/bin/ld: .obj/f7-firmware//home/jakob/Code/flipperzero-firmware/applications/applications.o:(.rodata.FLIPPER_PLUGINS+0x3c): undefined reference to \`hello_world_app'
collect2: error: ld returned 1 exit status
make[1]: *** [/home/jakob/Code/flipperzero-firmware/make/rules.mk:48: .obj/f7-firmware/firmware.elf] Error 1
make[1]: Leaving directory '/home/jakob/Code/flipperzero-firmware/firmware'
make: *** [Makefile:68: firmware_all] Error 2
```

If we then add the file and compile,

```
...
\tLD\t .obj/f7-firmware/firmware.elf
   text    data     bss     dec     hex filename
 883840    1696  362691 1248227  130be3 .obj/f7-firmware/firmware.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
16361308        dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
885853  dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
```

we can see that the impact on size (of the `.dfu`) is negligible. The overhead is less than than 0.01%.

### Injecting a Static Library

For this evaluation, I didn't bother with integrating everything nicely with the build system. I built a `staticlib` with `cargo build --release --target thumbv7em-none-eabihf` and hard-coded it into the object list that's passed to the linker. Hence, no diff is included here. We'll also need to augment the `LDFLAGS` to allow for redefinitions or else we get cacophony.

```
\tLD\t .obj/f7-firmware/firmware.elf
/opt/gcc-arm-none-eabi-10.3-2021.10/bin/../lib/gcc/arm-none-eabi/10.3.1/../../../../arm-none-eabi/bin/ld: /opt/gcc-arm-none-eabi-10.3-2021.10/bin/../lib/gcc/arm-none-eabi/10.3.1/thumb/v7e-m+fp/hard/libgcc.a(_arm_cmpdf2.o): in function \`__aeabi_dcmpeq':
(.text+0xac): multiple definition of \`__aeabi_dcmpeq'; ../applications/hello_world/target/thumbv7em-none-eabihf/release/libhello_world.a(compiler_builtins-50ab11bfb8346963.compiler_builtins.a2c45ff5-cgu.108.rcgu.o):/cargo/registry/src/github.com-1ecc6299db9ec823/compiler_builtins-0.1.71/src/macros.rs:335: first defined here
...
/opt/gcc-arm-none-eabi-10.3-2021.10/bin/../lib/gcc/arm-none-eabi/10.3.1/../../../../arm-none-eabi/bin/ld: /opt/gcc-arm-none-eabi-10.3-2021.10/bin/../lib/gcc/arm-none-eabi/10.3.1/thumb/v7e-m+fp/hard/libgcc.a(_arm_addsubdf3.o): in function \`__floatundidf':
(.text+0x30c): multiple definition of \`__aeabi_ul2d'; ../applications/hello_world/target/thumbv7em-none-eabihf/release/libhello_world.a(compiler_builtins-50ab11bfb8346963.compiler_builtins.a2c45ff5-cgu.143.rcgu.o):/cargo/registry/src/github.com-1ecc6299db9ec823/compiler_builtins-0.1.71/src/macros.rs:241: first defined here
```

Once all is squared away, we have a firmware image we can measure.

```
\tLD\t .obj/f7-firmware/firmware.elf
   text    data     bss     dec     hex filename
 887640    1696  358891 1248227  130be3 .obj/f7-firmware/firmware.elf
akob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
16447736        dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
889653  dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
```

The size impact (again, for the `.dfu`), is not as negligible. The overhead is about 0.4%.

Why is there a difference? The real answer follows later in the article – qI won't spoil it now. For, I'll leave it at "well, we can inspect the `.a` to see that it's including seemingly redundant/irrelevant stuff."

```
jakob@Epsilon ~/Code/flipperzero-firmware $ ar t applications/hello_world/target/thumbv7em-none-eabihf/release/libhello_world.a
hello_world-260792d958918d7e.hello_world.d3378d15-cgu.0.rcgu.o
compiler_builtins-50ab11bfb8346963.compiler_builtins.a2c45ff5-cgu.0.rcgu.o
...
absvdi2.o
absvsi2.o
absvti2.o
addvdi3.o
addvsi3.o
addvti3.o
aeabi_cdcmpeq_check_nan.o
aeabi_cfcmpeq_check_nan.o
aeabi_div0.o
aeabi_drsub.o
aeabi_frsub.o
bswapdi2.o
bswapsi2.o
clzdi2.o
...
udivmodsi4.o
udivsi3.o
umodsi3.o
apple_versioning.o
rustc_std_workspace_core-9ae03c706dab5b95.rustc_std_workspace_core.86fb1412-cgu.0.rcgu.o
core-4c81109e506dada5.core.1ad88dac-cgu.0.rcgu.o
```

This is quite a bit. I had to omit about a hundred other `compiler_builtins` objects. I came across this [Rust Internals post](https://internals.rust-lang.org/t/rust-staticlibs-and-optimizing-for-size/5746) from five years about using LTO to minimize `staticlib` size. Let's give it a try… first, adding `-flto` to `LDFLAGS` …

```
\tLD\t .obj/f7-firmware/firmware.elf
   text    data     bss     dec     hex filename
 887640    1696  358891 1248227  130be3 .obj/f7-firmware/firmware.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
16447736        dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
889653  dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
```

No difference. Let's try with Rust:

```
[profile.release]
lto = true
```

```
\tLD\t .obj/f7-firmware/firmware.elf
   text    data     bss     dec     hex filename
 887640    1696  358891 1248227  130be3 .obj/f7-firmware/firmware.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
16447736        dist/f7/flipper-z-f7-firmware-local-9d46f62b.elf
jakob@Epsilon ~/Code/flipperzero-firmware $ du -b dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
889653  dist/f7/flipper-z-f7-full-local-9d46f62b.dfu
```

No difference. So we'll press with the first approach of linking in an object file. This means we won't be able to use (at least not conveniently) a `build.rs` to automate making bindings, but we can still do that from the command-line.

## Binding to the Flipper API

We'll use [rust-bindgen](https://rust-lang.github.io/rust-bindgen/), which allows us to parse the C/C++ header files in the source tree and generate Rust FFI bindings automatically. We can get it by running `cargo install bindgen`. My approach was to `cat` together all the headers we care about run `bindgen` on that.

Where `CFLAGS` is something I got `make` to output using a few conveniently-placed `echo` 's – I had to trim it to just the `-D` declarations and `-I` include paths because `bindgen` doesn't seem to like any other Clang arguments. And because we can't use a crate to deal with the FFI types, I'm leveraging the nightly-only `core_ffi_c` API. Our code changes somewhat:

```
#![no_std]
#![feature(core_ffi_c)]
#![crate_type = "staticlib"]

use core::panic::PanicInfo;

include!("master.rs");

#[no_mangle]
pub extern "C" fn hello_world_app() -> i32 {
    0
}

/// This function is called on panic.
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```

.. surprisingly (at least, to me) the binary size doesn't change when I re-compile with the `include!` statement. I suppose that means that either dead code elimination is working as intended, or the code emitted by `bindgen` is just declarations and absolutely nothing else.

It does take a while for the compiler to stop spitting things out to my terminal, however.

```
...

warning: constant \`ViewDispatcherType_ViewDispatcherTypeFullscreen\` should have an upper case name
     --> master.rs:41909:11
      |
41909 | pub const ViewDispatcherType_ViewDispatcherTypeFullscreen: ViewDispatcherType = 2;
      |           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ help: convert the identifier to upper case: \`VIEW_DISPATCHER_TYPE_VIEW_DISPATCHER_TYPE_FULLSCREEN\`

warning: 8304 warnings emitted
```

Jesus.

## Writing Hello World

I chose to rewrite (most of) `applications/about/about.c`, as it seemed most amenable to being adapted into a "hello world" program. This might have been a good opportunity to use [C2Rust](https://c2rust.com/), but I didn't think it was worth learning the tool just to save me 200 lines of code.

```
#![no_std]
#![feature(core_ffi_c)]
#![crate_type = "staticlib"]

use core::panic::PanicInfo;

// Include the Rust code emitted by bindgen.
include!("master.inc");

/// Populate \`dest\` with the C string encoding of \`src\`.
unsafe fn write_cstr(dest: &mut [i8], src: &str) {
    let mut i = 0;
    for byte in src.bytes() {
        // Had to use unchecked because we don't have
        // \`core::panicking::panic_bounds_check\`. Hence, this whole function is
        // \`unsafe\`.
        *dest.get_unchecked_mut(i) = byte as i8;
        // dest[i] = byte as i8;
        i += 1;
    }
    dest[i] = '\0' as i8;
}

#[no_mangle]
pub unsafe extern "C" fn hello_world_app(_p: *const core::ffi::c_void) -> i32 {
    // Stack-allocated buffers for our converted C strings.
    let mut buffer: [i8; 64] = [0; 64];
    let mut buffer2: [i8; 64] = [0; 64];

    write_cstr(&mut buffer, "dialogs");
    let dialogs = core::mem::transmute::<*mut core::ffi::c_void, *mut DialogsApp>(
        furi_record_open(buffer[..].as_ptr()),
    );
    let message: *mut DialogMessage = dialog_message_alloc();

    write_cstr(&mut buffer, "gui");
    let gui = core::mem::transmute::<*mut core::ffi::c_void, *mut Gui>(furi_record_open(
        buffer[..].as_ptr(),
    ));
    let view_dispatcher: *mut ViewDispatcher = view_dispatcher_alloc();
    let empty_screen: *mut EmptyScreen = empty_screen_alloc();
    let empty_screen_index: u32 = 0;

    // draw empty screen to prevent menu flickering
    view_dispatcher_add_view(
        view_dispatcher,
        empty_screen_index,
        empty_screen_get_view(empty_screen),
    );
    view_dispatcher_attach_to_gui(
        view_dispatcher,
        gui,
        ViewDispatcherType_ViewDispatcherTypeFullscreen,
    );
    view_dispatcher_switch_to_view(view_dispatcher, empty_screen_index);

    let mut screen_index = 0;
    let about_screens = [unleashed_info_screen, unleashed_info_screen2];
    let about_screens_count = 2;

    loop {
        if screen_index >= about_screens_count - 1 {
            write_cstr(&mut buffer, "Back");
            dialog_message_set_buttons(
                message,
                buffer[..].as_ptr(),
                core::ptr::null_mut(),
                core::ptr::null_mut(),
            );
        } else {
            write_cstr(&mut buffer, "Back");
            write_cstr(&mut buffer2, "Next");
            dialog_message_set_buttons(
                message,
                buffer[..].as_ptr(),
                core::ptr::null_mut(),
                buffer2[..].as_ptr(),
            );
        }

        // let screen_result = about_screens[screen_index](dialogs, message);
        let screen_result = about_screens.get_unchecked(screen_index)(dialogs, message);

        if screen_result == DialogMessageButton_DialogMessageButtonLeft {
            if screen_index <= 0 {
                break;
            } else {
                screen_index -= 1;
            }
        } else if screen_result == DialogMessageButton_DialogMessageButtonRight {
            if screen_index < about_screens_count {
                screen_index += 1;
            }
        } else if screen_result == DialogMessageButton_DialogMessageButtonBack {
            break;
        }
    }

    dialog_message_free(message);
    write_cstr(&mut buffer, "dialogs");
    furi_record_close(buffer[..].as_ptr());

    view_dispatcher_remove_view(view_dispatcher, empty_screen_index);
    view_dispatcher_free(view_dispatcher);
    empty_screen_free(empty_screen);
    write_cstr(&mut buffer, "dialogs");
    furi_record_close(buffer[..].as_ptr());

    0
}

/// This function is called on panic.
#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}

unsafe fn unleashed_info_screen(
    dialogs: *mut DialogsApp,
    message: *mut DialogMessage,
) -> DialogMessageButton {
    let mut result: DialogMessageButton;

    let screen_header = "Hello from Rust\n";

    let mut buffer: [i8; 64] = [0; 64];

    write_cstr(&mut buffer, screen_header);
    dialog_message_set_header(
        message,
        buffer[..].as_ptr(),
        0,
        0,
        Align_AlignLeft,
        Align_AlignTop,
    );
    write_cstr(&mut buffer, screen_header);
    dialog_message_set_text(
        message,
        buffer[..].as_ptr(),
        0,
        26,
        Align_AlignLeft,
        Align_AlignTop,
    );
    result = dialog_message_show(dialogs, message);
    dialog_message_set_header(
        message,
        core::ptr::null_mut(),
        0,
        0,
        Align_AlignLeft,
        Align_AlignTop,
    );
    dialog_message_set_text(
        message,
        core::ptr::null_mut(),
        0,
        0,
        Align_AlignLeft,
        Align_AlignTop,
    );

    result
}

unsafe fn unleashed_info_screen2(
    dialogs: *mut DialogsApp,
    message: *mut DialogMessage,
) -> DialogMessageButton {
    let mut result: DialogMessageButton;

    let screen_header = "Hello from Rust 2\n";

    let mut buffer: [i8; 64] = [0; 64];

    write_cstr(&mut buffer, screen_header);
    dialog_message_set_header(
        message,
        buffer[..].as_ptr(),
        0,
        0,
        Align_AlignLeft,
        Align_AlignTop,
    );
    write_cstr(&mut buffer, screen_header);
    dialog_message_set_text(
        message,
        buffer[..].as_ptr(),
        0,
        26,
        Align_AlignLeft,
        Align_AlignTop,
    );
    result = dialog_message_show(dialogs, message);
    dialog_message_set_header(
        message,
        core::ptr::null_mut(),
        0,
        0,
        Align_AlignLeft,
        Align_AlignTop,
    );
    dialog_message_set_text(
        message,
        core::ptr::null_mut(),
        0,
        0,
        Align_AlignLeft,
        Align_AlignTop,
    );

    result
}
```

This is perhaps the worst Rust code I've ever written, but it's at least a starting point.

You may notice my comment about having "to use `unchecked_get` because we don't have `core::panicking::panic_bounds_check`." Indeed, when I naïvely decided to use `rustc` on its own, I was [actually eschewing](https://github.com/rust-lang/compiler-builtins/issues/245) some important parts of `libcore`. Right now, we're more "on our own" than we usually would be writing `no_std` code. The rest of the snippet is wrought with unsafe transmutation of `void *` values that are being passed across the FFI boundary, and some gross manual conversion (`write_cstr`) between Rust and C strings because I'm missing out on the niceness of `std` 's `CString` & friends. But it works.

… well, not without some tinkering. When I first tried to test this, I ran into an issue that made me think my USB-C cable was bad: qFlipper was failing to flash my firmware images, but the failure was happening while it was erasing the internal memory – before it could even get to sending my firmware image over the wire.

```
20235 [DBG] Erasing memory: 94%
20329 [DBG] Erasing memory: 95%
20423 [DBG] Erasing memory: 96%
20517 [DBG] Erasing memory: 97%
20658 [DBG] Erasing memory: 98%
20843 [REG] Device went offline: VID_0x483:PID_0xdf11
21958 [DBG] Failed to perform control transfer: LIBUSB_ERROR_NO_DEVICE
21958 [DBG] Unable to get device status
21958 [DBG] An error has occured during erase phase
21958 [DBG] Failed to erase page
21958 [DBG] Failed to erase the memory
21958 [RCY] Firmware Download @Ellwt1n3 ERROR: Can't flash firmware: An error has occured during the operation.
21958 [DEV] Firmware install from file @Ellwt1n3 ERROR: Can't flash firmware: An error has occured during the operation.
21958 [BKD] Current operation finished with error: "Can't flash firmware: An error has occured during the operation."
26905 [APP] qFlipper exited
26924 [default] "There are still \"1\" items in the process of being created at engine destruction."
```

I tried several times with several different USB-C cables, but the error occurred consistently, so I went to the [Flipper Zero forums to ask about it](https://forum.flipperzero.one/t/device-disconnect-when-erasing-memory/3731/2). Astra was kind enough to let me know that my firmware was too big. I thought this would mean having to golfing the code down to size, but really, no amount of optimization was going to make that "hello world" any smaller. The firmware image is big enough as it is, so I disabled a couple features I wasn't using at the time to make room.

```
diff --git a/applications/applications.mk b/applications/applications.mk
index baeaf4ae..8096cc08 100644
--- a/applications/applications.mk
+++ b/applications/applications.mk
@@ -35,8 +35,8 @@ SRV_STORAGE   = 1
 # Apps
 SRV_DESKTOP    = 1
 APP_ARCHIVE    = 1
-APP_GPIO       = 1
-APP_IBUTTON    = 1
+APP_GPIO       = 0
+APP_IBUTTON    = 0
 APP_INFRARED   = 1
 APP_LF_RFID    = 1
 APP_NFC                = 1
@@ -46,16 +46,16 @@ APP_PASSPORT = 1
 APP_UPDATER = 1

 # Custom apps
-APP_UNIRFREMIX = 1
-APP_CLOCK = 1
-APP_SPECTRUM_ANALYZER = 1
+APP_UNIRFREMIX = 0
+APP_CLOCK = 0
+APP_SPECTRUM_ANALYZER = 0

 # Plugins
-APP_MUSIC_PLAYER = 1
-APP_SNAKE_GAME = 1
-APP_WAV_PLAYER = 1
+APP_MUSIC_PLAYER = 0
+APP_SNAKE_GAME = 0
+APP_WAV_PLAYER = 0
 APP_HELLO_WORLD = 1
-APP_TETRIS_GAME = 1
+APP_TETRIS_GAME = 0

 # Debug
 APP_ACCESSOR = 1
```

![flipper-zero-hello-world.jpg](https://jakob.space/blog/flipper-zero-hello-world.jpg)

Figure 1: Black Flipper Zero reading "Hello from Rust".

And I was finally greeted by my Flipper.

## Conclusion

We now have a successful proof-of-concept for running Rust code on the Flipper Zero, even if it came at the cost losing Rust's distinctive safety features. We also learned that we'll continue to be constrained by size limitations as we continue to work on the firmware.

When we revisit this project next, we will:

1. Port these hacks over to the new `scons` build system.
2. Re-implement the parts of `libcore` necessary to leverage Rust's safety features.
3. Learn the Flipper's GUI library and develop a simple hex editor.

I'd like to develop a POCSAG beacon and a mechanism for loading code from the SD card rather than having everything baked into internal memory, but these are more ambitious, so I'll shelve them for a third article.

—

## Footnotes:

<sup><a href="https://jakob.space/blog/#fnr.1" role="doc-backlink">1</a></sup>

Which, I'll admit, I haven't actually read yet. I'd only skimmed the first chapter to get started. I'm going into this mostly blind.

<sup><a href="https://jakob.space/blog/#fnr.2" role="doc-backlink">2</a></sup>

My understanding is that this isn't good to do in general. I could [halt](https://en.wikipedia.org/wiki/Halt_and_Catch_Fire_\(computing\)) here, but I'm purposefully delaying the inevitable use of [inline assembly](https://doc.rust-lang.org/nightly/rust-by-example/unsafe/asm.html).

<sup><a href="https://jakob.space/blog/#fnr.3" role="doc-backlink">3</a></sup>

For those following along at home: I am building atop Eng1n33r's [Unleashed](https://github.com/Eng1n33r/flipperzero-firmware), rather than the official [flipperzero-firmware](https://github.com/flipperdevices/flipperzero-firmware).

<sup><a href="https://jakob.space/blog/#fnr.4" role="doc-backlink">4</a></sup>

My justification for this claim is this comment in the [New Firmware Update System](https://blog.flipperzero.one/new-firmware-update-system/) update: "a classic OTA Update is impossible for Flipper Zero. Flipper Zero's firmware is about 700KB in size and together with the Radio Stack the memory is completely exhausted. Since we don't have free space to work with, the classic OTA update won't work in our case."