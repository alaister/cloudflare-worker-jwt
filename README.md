# Cloudflare Worker JWT

A lightweight JWT implementation with ZERO dependencies for Cloudflare Workers.


## Contents

- [Install](#install)
- [Examples](#examples)
- [Usage](#usage)


## Install

```
npm i -D @tsndr/cloudflare-worker-jwt
```


## Examples

### Basic Example

```javascript
async () => {
    const jwt = require('@tsndr/cloudflare-worker-jwt')

    // Creating a token
    const token = await jwt.sign({ name: 'John Doe', email: 'john.doe@gmail.com' }, 'secret')

    // Verifing token
    const isValid = await jwt.verify(token, 'secret')

    // Check for validity
    if (!isValid)
        return

    // Decoding token
    const payload = jwt.decode(token)
}
```

### Restrict Timeframe

```javascript
async () => {
    const jwt = require('@tsndr/cloudflare-worker-jwt')

    // Creating a token
    const token = await jwt.sign({
        name: 'John Doe',
        email: 'john.doe@gmail.com',
        nbf: Math.floor(Date.now() / 1000) + (60 * 60),      // Not before: Now + 1h
        exp: Math.floor(Date.now() / 1000) + (2 * (60 * 60)) // Expires: Now + 2h
    }, 'secret')

    // Verifing token
    const isValid = await jwt.verify(token, 'secret') // false

    // Check for validity
    if (!isValid)
        return

    // Decoding token
    const payload = jwt.decode(token) // { name: 'John Doe', email: 'john.doe@gmail.com', ... }
}
```

## Usage

<hr>

### `jwt.sign(payload, secret, [options])`

Signs a payload and returns the token.

#### Arguments

Argument    | Type     | Satus    | Default | Description
----------- | -------- | -------- | ------- | -----------
`payload`   | `object` | required | -       | The payload object. To use `nbf` (Not Before) and/or `exp` (Expiration Time) add `nbf` and/or `exp` to the payload.
`secret`    | `string` | required | -       | A string which is used to sign the payload.
`options`   | `object`, `string` | optional | `{ algorithm: 'HS256' }` | The options object supporting `algorithm` and `keyid` or just the algorithm string. (See [Available Algorithms](#available-algorithms))

#### `return`
Returns token as a `string`.

<hr>

### `jwt.verify(token, secret, [options])`

Verifies the integrity of the token and returns a boolean value.

Argument    | Type     | Satus    | Default | Description
----------- | -------- | -------- | ------- | -----------
`token`     | `string` | required | -       | The token string generated by `jwt.sign()`.
`secret`    | `string` | required | -       | The string which was used to sign the payload.
`algorithm` | `object`, `string` | optional | `{ algorithm: 'HS256' }` | The options object supporting `algorithm` or just the algorithm string. (See [Available Algorithms](#available-algorithms))

#### `return`
Returns `true` if signature, `nbf` (if set) and `exp` (if set) are valid, otherwise returns `false`. 

<hr>

### `jwt.decode(token)`

Returns the payload **without** verifying the integrity of the token. Please use `jwt.verify()` first to keep your application secure!

Argument    | Type     | Satus    | Default | Description
----------- | -------- | -------- | ------- | -----------
`token`     | `string` | required | -       | The token string generated by `jwt.sign()`.

#### `return`
Returns payload `object`.

### Available Algorithms
 - ES256
 - ES384
 - ES512
 - HS256
 - HS384
 - HS512
 - RS256
 - RS384
 - RS512