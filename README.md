# serde-crypt

[![Build Status][action-badge]][action-url]
[![Crate Docs][docs-badge]][docs-url]
[![Crate Version][crates-badge]][crates-url]
[![Crate Coverage][coverage-badge]][coverage-url]

[action-badge]: https://img.shields.io/github/actions/workflow/status/D3PSI/serde-crypt/build.yaml?branch=master&label=build&logo=github&style=flat-square
[action-url]: https://github.com/D3PSI/serde-crypt/actions/workflows/build.yaml
[crates-badge]: https://img.shields.io/crates/v/serde-crypt.svg?logo=rust&style=flat-square
[crates-url]: https://crates.io/crates/serde-crypt
[docs-badge]: https://img.shields.io/docsrs/serde-crypt?logo=Docs.rs&style=flat-square
[docs-url]: http://docs.rs/serde-crypt
[coverage-badge]: https://img.shields.io/codecov/c/github/D3PSI/serde-crypt?logo=codecov&logoColor=white&style=flat-square
[coverage-url]: https://app.codecov.io/gh/D3PSI/serde-crypt

The end-to-end encrypted `serde::Serializer` and `serde::Deserializer`.
**wasm-ready**.

### Example

```rust
use ring::rand::{SecureRandom, SystemRandom};
use serde::{Deserialize, Serialize};
use serde_crypt::{setup, MASTER_KEY_LEN};

#[derive(Serialize, Deserialize, Debug, PartialEq, Eq)]
struct Example {
    #[serde(with = "serde_crypt")]
    private: String,
    public: String,
}

fn main() -> Result<(), serde_json::Error> {
    let mut key: [u8; MASTER_KEY_LEN] = [0; MASTER_KEY_LEN];
    let rand_gen = SystemRandom::new();
    rand_gen.fill(&mut key).unwrap();

    setup(key);
    let data = Example {
        private: "private data".to_string(),
        public: "public data".to_string(),
    };

    let serialized = serde_json::to_string(&data).unwrap();
    let deserialized: Example = serde_json::from_str(&serialized).unwrap();

    assert_eq!(deserialized, data);
    Ok(())
}
```
