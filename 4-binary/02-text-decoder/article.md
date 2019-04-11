# TextDecoder и TextEncoder

Что если бинарные данные фактически являются строкой? На пример, мы получили файл с текстовыми данными.

Встроенный [TextDecoder](https://encoding.spec.whatwg.org/#interface-textdecoder) объект позволяет читать данные внутри Javascript строки в буфер и декодировать их.

Прежде всего нам нужно создать:
```js
let decoder = new TextDecoder([label], [options]);
```

- **`label`** -- тип кодировки, `utf-8`используется по умолчанию, но также поддерживаются `big5`, `windows-1251` и многие другие.
- **`options`** -- необязательный объект с дополнительными опциями:
  - **`fatal`** -- boolean, если `true` тогда генерируется ошибка для невалидных (не декодируемых) символов, в ином случае (по умолчанию) они заменяются символом `\uFFFD`.
  - **`ignoreBOM`** -- boolean, если `true` тогда игнорируется BOM (дополнительная byte-order unicode метка), необходимо крайне редко.

...And then decode:

```js
let str = decoder.decode([input], [options]);
```

- **`input`** -- `BufferSource` to decode.
- **`options`** -- optional object:
  - **`stream`** -- true for decoding streams, when `decoder` is called repeatedly with incoming chunks of data. In that case a multi-byte character may occasionally split between chunks. This options tells `TextDecoder` to memorize "unfinished" characters and decode them when the next chunk comes.

For instance:

```js run
let uint8Array = new Uint8Array([72, 101, 108, 108, 111]);

alert( new TextDecoder().decode(uint8Array) ); // Hello
```


```js run
let uint8Array = new Uint8Array([228, 189, 160, 229, 165, 189]);

alert( new TextDecoder().decode(uint8Array) ); // 你好
```

We can decode a part of the buffer by creating a subarray view for it:


```js run
let uint8Array = new Uint8Array([0, 72, 101, 108, 108, 111, 0]);

// the string is in the middle
// create a new view over it, without copying anything
let binaryString = uint8Array.subarray(1, -1);

alert( new TextDecoder().decode(binaryString) ); // Hello
```

## TextEncoder

[TextEncoder](https://encoding.spec.whatwg.org/#interface-textencoder) does the reverse thing -- converts a string into bytes.

The syntax is:

```js run
let encoder = new TextEncoder();
```

The only encoding it supports is "utf-8".

It has two methods:
- **`encode(str)`** -- returns `Uint8Array` from a string.
- **`encodeInto(str, destination)`** -- encodes `str` into `destination` that must be `Uint8Array`.

```js run
let encoder = new TextEncoder();

let uint8Array = encoder.encode("Hello");
alert(uint8Array); // 72,101,108,108,111
```
