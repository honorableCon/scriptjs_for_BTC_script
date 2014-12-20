bitcoin-script
======

JavaScript implementation of Script, Bitcoin's scripting language, along with a Script Playground, deployed [here](http://www.crmarsh.com/script-playground/). See my [blog post](crmarsh.com/script/) for more.

The original ES6 source can be found on [GitHub](https://github.com/crm416/script).

## Usage

This package can be used to evaluate Bitcoin scripts as follows:

```js
var evaluate = require('bitcoin-script').evaluate;
var script = 'OP_1 OP_VERIFY';
evaluate(script);
// => true
```

(Note: here, `OP_VERIFY` could be excluded, as default behavior is to check for a 1 on the top of the stack if there's no terminating command in the script.)

Alternatively, you can use the lock-unlock paradigm, which concatenates the scripts before evaluating:

```js
var unlock = require('bitcoin-script').unlock;
var scriptSig = 'OP_1';
var scriptPubKey = 'OP_VERIFY';
unlock(scriptSig, scriptPubKey);
// => true
```

Further [examples](https://github.com/crm416/script/tree/master/src/examples) and [tests](https://github.com/crm416/script/tree/master/src/__tests__) are available in the [GitHub repo](https://github.com/crm416/script/).

## How It Works

(A longer explanation of Script and this implementation can be found in my [blog post](crmarsh.com/script/).)

Script programs are compiled to JavaScript using [Jison](http://zaach.github.io/jison/), with behavior following the specification outlined on the [Bitcoin Wiki](https://en.bitcoin.it/wiki/Script). For those unfamiliar, Script is a simple, stack-based programming language used to validate transactions in the Bitcoin protocol.

When the parser runs over a Script program, it returns an object of the following form:

```js
{
    value: [value],
    code: [code]
}
```

This allows for inspection into the compiled code (e.g., for use in the live editor), as well as the return value (`true` or `false`) of the Script.

### Deviations From the Spec

Note that as this is an educational tool, the goal is _not_ to create a full-fidelity re-implementation of Script, but rather, to focus on re-creating Script's core behavior. With that in mind, the implementation here differs from that described on the Wiki in a few ways. For example:

- Unlike in the official implementation, OP_CHECKMULTISIG does _not_ pop an extra, arbitrary value off the stack (as this is considered a bug and would only serve to confuse new users).
- Signatures aren't generated by hashing transaction inputs and outputs (as the snippets only exist in isolation); instead, the protocol expects users to sign a simple nonce (in this case, the string 'Secure').
- Any hex data is pushed to the state with no limitations.

Operations labeled as _Disabled_ are not implemented.

## License

MIT.