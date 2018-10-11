
## 将文件下载到临时目录

[![reqwest-badge]][reqwest] [![tempdir-badge]][tempdir] [![cat-net-badge]][cat-net] [![cat-filesystem-badge]][cat-filesystem]

创建一个临时目录[`TempDir::new`]并通过HTTP同步下载文件[`reqwest::get`].

创建目标[`File`]名称来自[`Response::url`]在内部[`TempDir::path`]并将下载的数据复制到其中.[`io::copy`]. 临时目录自动删除.`run`函数返回.

```rust,no_run
# #[macro_use]
# extern crate error_chain;
extern crate reqwest;
extern crate tempdir;

use std::io::copy;
use std::fs::File;
use tempdir::TempDir;
#
# error_chain! {
#     foreign_links {
#         Io(std::io::Error);
#         HttpRequest(reqwest::Error);
#     }
# }

fn run() -> Result<()> {
    let tmp_dir = TempDir::new("example")?;
    let target = "https://www.rust-lang.org/logos/rust-logo-512x512.png";
    let mut response = reqwest::get(target)?;

    let mut dest = {
        let fname = response
            .url()
            .path_segments()
            .and_then(|segments| segments.last())
            .and_then(|name| if name.is_empty() { None } else { Some(name) })
            .unwrap_or("tmp.bin");

        println!("file to download: '{}'", fname);
        let fname = tmp_dir.path().join(fname);
        println!("will be located under: '{:?}'", fname);
        File::create(fname)?
    };
    copy(&mut response, &mut dest)?;
    Ok(())
}
#
# quick_main!(run);
```

[`file`]: https://doc.rust-lang.org/std/fs/struct.File.html

[`io::copy`]: https://doc.rust-lang.org/std/io/fn.copy.html

[`reqwest::get`]: https://docs.rs/reqwest/*/reqwest/fn.get.html

[`response::url`]: https://docs.rs/reqwest/*/reqwest/struct.Response.html#method.url

[`tempdir::new`]: https://docs.rs/tempdir/*/tempdir/struct.TempDir.html#method.new

[`tempdir::path`]: https://docs.rs/tempdir/*/tempdir/struct.TempDir.html#method.path