# Simple AWS Lambda in Rust

This is a simple AWS lambda written in Rust. It is heavily borrowed from 
the sources listed in the references section. The idea is to put it all
together in a working package so one can get started quickly.

## Prerequisites

* Assumes you are running on MacOS.
* You must create an AWS execution role (say `RustLambdaRole`) with the following policies:
    * AWSLambdaBasicExecutionRole
    * AWSXrayWriteOnlyAccess
* Gather your AWS Account ID

## To build

```shell script
$ rustup target add x86_64-unknown-linux-musl
$ brew install filosottile/musl-cross/musl-cross
$ mkdir .cargo
$ echo '[target.x86_64-unknown-linux-musl] linker = "x86_64-linux-musl-gcc"' > .cargo/config
$ cargo build --release --target x86_64-unknown-linux-musl
$ zip -j rust.zip ./target/x86_64-unknown-linux-musl/release/bootstrap
```

## To deploy

```shell script
$ aws lambda create-function --function-name rustTest \
  --handler doesnt.matter \
  --zip-file file://./rust.zip \
  --runtime provided \
  --role arn:aws:iam::ACCOUNT_ID:role/ROLE_NAME \
  --environment Variables={RUST_BACKTRACE=1} \
  --tracing-config Mode=Active

$ aws lambda invoke --function-name rustTest \
  --payload '{"firstName": "world"}' \
  output.json
```

## References

* https://aws.amazon.com/blogs/opensource/rust-runtime-for-aws-lambda/
* https://crates.io/crates/lambda_runtime