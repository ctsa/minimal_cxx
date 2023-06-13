### Minimal demonstration of cxx static build issue

The following crate minimally adds the cxx crate. It compiles/runs using a standard build:

```
$ cargo run
    Updating crates.io index
   Compiling cc v1.0.79
   Compiling proc-macro2 v1.0.60
   Compiling unicode-ident v1.0.9
   Compiling quote v1.0.28
   Compiling cxxbridge-flags v1.0.95
   Compiling link-cplusplus v1.0.8
   Compiling cxx v1.0.95
   Compiling syn v2.0.18
   Compiling cxxbridge-macro v1.0.95
   Compiling minimal_cxx v0.1.0 (/Users/csaunders/devel/github/minimal_cxx)
    Finished dev [unoptimized + debuginfo] target(s) in 6.16s
     Running `target/debug/minimal_cxx`
Hello, world!
```

However when attempting to compile a static linux binary, the build fails. The snippet provided below has most of the paths stripped out but gives the errors:

```
$ RUSTFLAGS='-C target-feature=+crt-static -C relocation-model=static' cargo run --target x86_64-unknown-linux-gnu
   Compiling proc-macro2 v1.0.60
   Compiling cc v1.0.79
   Compiling unicode-ident v1.0.9
   Compiling quote v1.0.28
   Compiling cxxbridge-flags v1.0.95
   Compiling link-cplusplus v1.0.8
   Compiling cxx v1.0.95
   Compiling syn v2.0.18
   Compiling cxxbridge-macro v1.0.95
   Compiling minimal_cxx v0.1.0 (/home/csaunders/devel/github/minimal_cxx)
error: linking with `cc` failed: exit status: 1
  |
  = note: binnowrap/x86_64-libc_2.17-linux-gnu-ld.bfd: /home/csaunders/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libstd-8389830094602f5a.rlib(std-8389830094602f5a.std.fd8b77e9-cgu.0.rcgu.o): in function `std::sys::unix::os::home_dir::fallback':
          /rustc/90c541806f23a127002de5b4038be731ba1458ca/library/std/src/sys/unix/os.rs:640: warning: Using 'getpwuid_r' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
          binnowrap/x86_64-libc_2.17-linux-gnu-ld.bfd: /home/csaunders/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libstd-8389830094602f5a.rlib(std-8389830094602f5a.std.fd8b77e9-cgu.0.rcgu.o): in function `<std::sys_common::net::LookupHost as core::convert::TryFrom<(&str,u16)>>::try_from::{{closure}}':
          /rustc/90c541806f23a127002de5b4038be731ba1458ca/library/std/src/sys_common/net.rs:206: warning: Using 'getaddrinfo' in statically linked applications requires at runtime the shared libraries from the glibc version used for linking
          binnowrap/x86_64-libc_2.17-linux-gnu-ld.bfd: attempted static link of dynamic object `lib64/libstdc++.so'
          binnowrap/x86_64-libc_2.17-linux-gnu-ld.bfd: /home/csaunders/.rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/x86_64-unknown-linux-gnu/lib/libstd-8389830094602f5a.rlib(std-8389830094602f5a.std.fd8b77e9-cgu.0.rcgu.o): undefined reference to symbol '__tls_get_addr@@GLIBC_2.3'
          binnowrap/x86_64-libc_2.17-linux-gnu-ld.bfd: lib64/ld-linux-x86-64.so.2: error adding symbols: DSO missing from command line
          collect2: error: ld returned 1 exit status
          
  = note: some `extern` functions couldn't be found; some native libraries may need to be installed or have their path specified
  = note: use the `-l` flag to specify native libraries to link
  = note: use the `cargo:rustc-link-lib` directive to specify the native libraries to link with Cargo (see https://doc.rust-lang.org/cargo/reference/build-scripts.html#cargorustc-link-libkindname)

error: could not compile `minimal_cxx` (bin "minimal_cxx") due to previous error
```


