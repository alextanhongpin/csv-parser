# Writing an intelligent CSV Parser

This requires the header to be present.
Transform created at to at created, date created

Transformation and normalisation. We normalise everything to snake casefirst. They have several advantage, they are lowercase, and we can split them nicely into tokens for substitution etc. 

Blacklisting. Tokens or characters that are not allowed should be removed.

Mapping. Mapping can only go in one direction, left to right. We need to make sure that we delete all keys that exists in values.
Synonyms
Abbreviations expansion, e.g nr. To number

Casing
Similarity/levenshtein distance.
Auto checking fields such as camel or snake

Empty values, skipped rows
- when there are empty values, warn use to fill them up, else skip the row
- if heard are not passed in, allow users to define the headers

Dealing with intelligent applications
- auto CSV extractor
- auto mapper
- frecency algorithm with time decay
- case detector
- scoring system for resume

```js
const toSnake = (camelCase) =>
  camelCase.replace(/[a-z][A-Z0-9]/g, (match) => match.toLowerCase().split('').join('_'))

const toCamel = (snakeCase) =>
  snakeCase.replace(/_([a-z0-9])/gi, (_match, p1) => p1.toUpperCase())

function letterCaseInsensitive(obj, dictionary = {}) {
  const handler = {
    get(target, prop, receiver) {
      const camelCaseProp = toCamel(prop)
      const snakeCaseProp = toSnake(prop)
      const mappedProp = dictionary[prop]
      return Reflect.get(target, prop, receiver) || Reflect.get(target, camelCaseProp, receiver) || Reflect.get(target, snakeCaseProp, receiver) || Reflect.get(target, mappedProp, receiver)
    }
  }
  const proxy = new Proxy(obj, handler)
  return proxy
}

const obj = letterCaseInsensitive({
  hello: 'world',
  meaningOfLife: 42,
  hello_world: 'greeting'
}, {
  hi: 'hello',
  hey: 'hello'
})

console.log(obj.hello_world, obj.helloWorld, obj.hi)
```
