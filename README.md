# workers-rs

This is a fork that is up to rebased up to [cloudflare/workers-rs](https://github.com/cloudflare/workers-rs/tree/12e011ad9fa2eddd73759908d9d54b5d54dd07ee).

The problem of the original repository is that it does not provide the Rust version of [`getAssetFromKV`](https://github.com/cloudflare/kv-asset-handler#getassetfromkv) function which exists in the JavaScript package, but this feature is required by our own `leptos-cloudflare` integration that uses KV to host static assets. For deployment, wrangler will derive hashes from worker site files and append it to the file name before the extension. For example, `client_bg.wasm` will become `client_bg.849eaf9261.wasm`. However, under local development, the file name does not contain a hash. These discrepancies need to be handled. `workers-rs` does not offer such a functionality natively. There's a related PR at https://github.com/cloudflare/workers-rs/pull/308 but it seems that Cloudflare team is not keen on merging the PR or even integrating this feature into workers-rs repository.

Therefore, this fork adds one more function to `ctx.env` from `ctx: worker::Context` which is a parameter to the worker handler function: `asset_key(&self, name: &str) -> worker::Result<String>`. This is used to retrieve the name of an asset uploaded to KV when the worker is deployed, or the local name of the asset if the worker is run locally. Note that this only works if [site](https://developers.cloudflare.com/workers/wrangler/configuration/#workers-sites) was configured in `wrangler.toml`.

The usage example can be found in the doc comments of `asset_key`.
