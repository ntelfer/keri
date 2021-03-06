---
tags: KERI
email: sam@samuelsmith.org
---

# Implementation Notes for KERI

https://github.com/decentralized-identity/keri

https://github.com/decentralized-identity/keripy

https://github.com/decentralized-identity/kerijs

https://github.com/decentralized-identity/keriox

https://github.com/decentralized-identity/keri/blob/master/implementation.md

https://github.com/SmithSamuelM/keri

https://hackmd.io/orhyiJkLT721v4PCPkvQiA?both

https://github.com/SmithSamuelM/keri/blob/master/implementation.md

smith.samuel.m@gmail.com

https://github.com/SmithSamuelM/Papers/blob/master/whitepapers/KERI_WP_2.x.web.pdf

https://github.com/SmithSamuelM/Papers/blob/master/presentations/KERI2_Overview_IIW_2020_A.pdf




## Things ToDo



1) Seal (Anchors) for arbitrary data:

  The interpretation of the data  associated with the digest or hash tree root in the seal is independent of KERI. This allows KERI to be agnostic about anchored data semantics. Another way of saying this is that seals are data agnostic; they don’t care about the semantics of its associated data. This better preserves privacy because the seal itself does not leak any information about the purpose or specific content of the associated data. Furthermore, because digests are a type of content address, they are self-discoverable. This means there is no need to provide any sort of context or content specific tag or label for the digests. Applications that use KERI may provide discovery of a digest via a hash table (mapping) whose indexes (hash keys) are the digests and the values in the table are the location of the digest in a specific event. To restate, the semantics of the digested data are not needed for discovery of  the digest within a key event sequence.
  
To elaborate, the provider of the data understands the purpose and semantics and may disclose those as necessary, but the act of verifying authoritative control does not depend on the data semantics merely the inclusion of the seal in an event. It’s up to the provider of the data to declare or disclose the semantics when used in an application. This may happen independently of verifying the authenticity of the data via the seal. This declaration may be provided by some external application programmer interface (API) that uses KERI. In this way, KERI provides support to applications that satisfies the spanning layer maxim of minimally sufficient means. Seals merely provide evidence of authenticity of the associated (anchored) data whatever that may be. 

This approach follows the design principle of context independent extensibility. Because the seals are context agnostic, the context is external to KERI. Therefore the context extensibility is external to and hence independent of KERI. This is in contrast to context dependent extensibility or even independently extensible contexts that use extensible context mechanisms such as linked data or tag registries [84; 101; 105; 135]. Context independent extensibility means that KERI itself is not a locus of coordination between contexts for anchored data. This maximizes decentralization and portability. Extensibility is provided instead at the application layer above KERI though context specific external APIs that reference KERI seals in order to establish control authority and hence authenticity of the anchored (digested) data. Each API provides the context not KERI. This means that interoperability within KERI is focused solely on interoperability of control establishment. But that interoperability is total and complete and is not dependent on anchored data context. This approach further reflects KERI’s minimally sufficient means design aesthetic. 


   A seal is an ordered self-describing data structure. Abstractly this means each element of the seal has a tag or label that describes the associated element’s value. A minimal seal must have an element whose label indicates that the value is either a digest or the root of a hash tree. In either case (digest or root) the value is fully qualified Base64 with a prepended derivation code that indicates the type of hash algorithm used to create the digest or hash root. When the seal is referring to another event it must also include the sequence number (sn) and identifier prefix  elements in order to indicate the location of the sealed event in its key event sequence. These element labels  (digest, root, prefix, sn) are so far the only normative labels needed to process a KERI seal. 
   The data structure that provides the elements of a seal must have a canonical order so that it may be reproduced in a digest of elements of a event. Canonical ordering may be provided by a list of single element mappings. The order of appearance of each {label: value} mapping in the list is standardized.
   
   Currently, three types of seals, digest, hash tree root (Merkle tree) and event.
 Both or fully qualified Base64 with prepended derivation code for hash algorithm
 
 
Anchors hash links not data format. Keep KERI pure (qua standard, and the logs it produces) no need to define tags in KERI
    
 Seals have labeled elements.
 Must have digest or root (hash tree root)element
 Event seal may also have sn and prefix elements.
 
 
 Hash trees are sparse trees with finite tree depth using depth prefix on internal hash nodes. These prevents 2nd preimage attacks. Similar to certificate transparency.
 
 Hyperledger Indy uses sparse Merkle tree library for revocation registry. Is that a good library to use for KERI Seal Merkle Trees?
 

    
    Discussion notes:
    - Mitwicki (HCF): we use VCs this way, to contain a minimal payload (hashlink). secure data storage or more public data hubs can be linked this way.
        - Sam: not just hashlink but an identifier that has to be dereferenced 
        - Robert: We're using Manu's hashlinks (DRI) because it contains its own crypto suite etc, but we're a little worried that it exposes these logs to some kinds of correlation attacks.
        - Nader: Here's the reference for that hashlink standard https://tools.ietf.org/html/draft-sporny-hashlink-04
        - Sam: Tag-dependent level of correlatability/lock-in to one encoding or dereferencing dependency
        - Robert: [In our usage of VCs?], we have been thinking about the discovery mechanism via DID Resolution (going from DID to DID:Doc to Service Endpoint [to agent and thus to vault/storage and returned resource]
    - 	Sam: Payloads should be encoding agnostic, mixing and matching should work This is because KERI only cares about the anchor/digest format. The anchored data is not transported in the KERI event so the anchored data can be any encoding.
    - 	
    - 	Mark L Smartopia: Kantara Initiative has been working on consent receipts along these lines, and standardizing on these [essentially legal] events across ontologies has been difficult; we're been able to standardize this and present it at ISO. A consent state record would be a notification and a receipt, and I think this could line up well; we have a GDPR delegation (in close dialogue w/w3c data privacy WG on ontology for this). In the last year, lots of standards have been completed and we're working towards a common record format (legally required to be open).
        - 	Sam: anchor/tag structure and tag-specific link out to data works with your logging needs?
        - 	Sam: a parser needs to know what is anchored by the anchor and how to consume/interpret the digest; the "tag name" is just for a KERI-level categorization of event types
        - 	Mark: we think of delegation as tripartite: regulator, subject, authority; our main data object is also tripartite: notice (event), notification (intermediary), receipt (record)
        - 	Sam: I think you might be imposing semantics from a similar but different definition of authority; I am trying to keep this super agnostic (and specifically about control authority events); 
        - 	Mark: Notary/notification ledger: legal notary + SSI-based ledger; 
        - 	Sam: is KERI preventing you from using KERI in this way? Is this a use-case and can KERI be used without adding anything to it? 
            - 	documentable as a use case for accompanying documentation? **TODO ITEM?**
        - 	Mark: 
        - 	Michael Gorlick: Digest format bears a resemblance to something I worked on IPFS
        - 	Sam: not coincidental! Based on that precedent
        - 	Mark: See also https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=coel
        - 	Nader: See also https://github.com/ipld/specs/blob/master/block-layer/content-addressable-archives.md for IPLD assembling hashes into a digest

 
    - 	Robert: Payload <----> tag relationship (discoverable semantics?) 
        - 	Sam: But I'm afraid that puts the cart in front of the horse and creates a dependency; I want the tag to define the form of the digest, but I want to indirect AWAY from that and force the semantics out to the reference; this is the secure overlay for the internet, not everybody's data format 
        - 	Charles: arbitrary data gets us away from minimal sufficiency
        - 	Sam: Just anchors, that's what i'm calling minimmaly sufficient, spec should not dictate anything about what is anchored, no ontological or semantic conditions or restraints
    - 	Robert: digest prefix ?
    - 	Sam: These list of these tags relates to the DID core spec registry of did doc properties; KERI only cares about the prefixes and assumes a namespaces of self-certifying identifiers (not just DIDs); KERI needs to be a secure spanning layer for identifiers that needs to be manually agnostic to secure events happening in specific namespaces/resolution spaces; 
    - stateless services and self-certifying identifiers
    - 	example: a self-certifying did wrapped in a keri DID:Un
```
did:un:AVrTkep6H-Qt27fThWoNZsa884HA8tr54sHON1vWl6FE/path?kkey=me#flab
```


2)  Version format
    Encoding formats: Json, MsgPack, CBOR, Binary
    
    - JSON vs JSON-LD: data referenced by the payload digest could be -LD; 
    - Robert: what's the idea of the representation of the version?
        - Sam: in my earlier protocol RAET, I used a short string prefix to mark the digest as JSON, MsgPack, Text, or Binary (//Unicode prefix); version-string should bear a prefix (even if redundant from context) in its first few bytes; 
        - Robert: Sam: MsgPack and CBOR are versioned, while JSON isnt, so this
        `- KERI 1.0.0 MsgPack 1.0`
        - Robert: MIME type? Sam: I'm open to it, MIME types don't take up that many bytes...
            - Charles: Restricting the MIME types to a specific subset would help; Sam: yup, each version of KERI should only support a finite list of MIME types (so that it doesn't need to be encoded in each event)
                - examples  `application/keri+json`, `application/keri+binary`

```bash

KERI_1.0_application/keri+json
KERI_1.0_application/keri+cbor
KERI_1.0_application/keri+msgpack
KERI_1.0_application/keri+binary
```

version 1.0 (1,0) (major,minor)
major is backward breaking 
minor is backward compatible

3)  Derivation codes
    Context plus code
    
    prefix
    digest of prev event
    public keys in array
    Next digest (threshold, public key array)
    delegation digests
    by tag data digests data anchor digests
    witnesses prefixes
    signatures
    
    
    Other codes
    
    See latest table in slide deck and paper
    Need to cread markdown table as reference
    
    Create KID for it
    
   
4) Languages: Python, Javascript (nodejs), Rust, Python +rust
    keripy, kerijs, keriox,

    Discussion:
    - Sam: Py
    - Spherity (hi!): nodeJS
    - Rust? JOLOCOM ftw (or at least Charles) "KeriOX"

    What about WASM rust version?
    Who wants to do this?

5) Derivation Extraction Library

    - library needs to turn each event into a digest
    - identifier derivated (base64) - non-inception: cyphersuite of public key; inception: need that data; 

    - tag data digest function probably requires a separate library (anchor sublibrary for each tag?)

6) Verification Engine

    - inception event populates state machine
    - derive crypto material, verify it all
    - each new event, derive material, update state
    - the more efficient the better to scale
        - witness validator controller, everyone runs this all the time
    - Charles: replaying key events (and checking the outcome?) --> authoritative key material at any given time
        - Charles: historical DID Document checking?

8) Database for KERLs: LMDB

    - Standardize on LMDB for v1 at least
        - used by (and hardened by) LDAP
        - LDAP license (//BSD + (C)) - pretty free
        - well-coded paragon implementations in every language
        - useful for recovery --> can have multiple entries for each key (appendonly allowing double-entries for forks if you use sequence numbers properly)

    https://github.com/LMDB/lmdb
    
    https://symas.com/lmdb/

8) Controller, Validator, Witness, Watcher, Juror, Judge
9) 
10) Clients: web, cmd, python, etc
    WASM rust library for web clients?

10) KERI DID method: DID:UN-method (universal but also undoer and destroyer of worlds)

    - Charles: un-conference friendly!


11) Delegation Permissions
    What sort of stuff do we need to permission?
    Nested delegation of new delegated identifiers?
    Delegation of only some data tags?
    
12)  KIDs  KERI Implementation/Improvement Documents
    added kids directory to keri repo.
    
13)  Ordered Mappings

see issue https://github.com/decentralized-identity/keri/issues/7


One of the complications of serialization of mappings is whether or not the elements of the
mapping appear in any given order. The best ordering is insertion order. This allows logical
ordering independent of lexicographic ordering of the labels.

With Javascript ES6  there is a new datatype called a Map that supports insertion or defined
ordered elements. 


This may be JSON encoded/decoded in an order preserving manner using the JSON.stringify and JSON.parse functions in javasript using the approach below.

Python has long had support for ordered dict objects and the various python json parsing libraries
preserve that ordering by default. Since Python 3.6 all dict  are ordered by default so its comes for
free.  In binary languages like Rust data structures are explicitly laid out so do not have the hash table problem of un predictable order that objects in Javascript and until recently dicts in Python had.

As far as I can tell the rust serde library allows ordered mapping to/from JSON serializations so its
not a problem in rust

Ruby since 2007 supports ordered Hash objects (dict equivalent)

I don't know about other languages like Go and Java.

The proposal is to impose an ordered mapping constraint on KERI event serializations and extracted subsets of KERI events. This is not a weak sorted ordering but a strong ordering (order of appearance).
We already have to do ordering for extracted sets of elements in order to compute digests. This may be done universally by using list of lists of (lable, value) pairs. But this is less programmer friendly
than using ordered Mappings (Map in JS ES6, dict in Python 3.6+) that serialize and deseralize in order.

In addition, automatic determination of serialization encoding becomes trivialized when the serializations are guaranteed to preserve the order of the elements. This means we can depend
on the version element being the first element serialized. So parsing the encoding means looking at the first few bytes to extract the version string which will always appear in an unambiguous location at the front of the serialization.



https://stackoverflow.com/questions/29085197/how-do-you-json-stringify-an-es6-map


Both JSON.stringify and JSON.parse support a second argument. replacer and reviver respectively. With replacer and reviver below it's possible to add support for native Map object, including deeply nested values

```javascript
function replacer(key, value) {
  const originalObject = this[key];
  if(originalObject instanceof Map) {
    return {
      dataType: 'Map',
      value: Array.from(originalObject.entries()), // or with spread: value: [...originalObject]
    };
  } else {
    return value;
  }
}
function reviver(key, value) {
  if(typeof value === 'object' && value !== null) {
    if (value.dataType === 'Map') {
      return new Map(value.value);
    }
  }
  return value;
}

Usage:

const originalValue = new Map([['a', 1]]);
const str = JSON.stringify(originalValue, replacer);
const newValue = JSON.parse(str, reviver);
console.log(originalValue, newValue);
Deep nesting with combination of Arrays, Objects and Maps

const originalValue = [
  new Map([['a', {
    b: {
      c: new Map([['d', 'text']])
    }
  }]])
];
const str = JSON.stringify(originalValue, replacer);
const newValue = JSON.parse(str, reviver);
console.log(originalValue, newValue);
```


## Collaboration

COLLAB LOGISTICS: SEE KERI GITHUB REPO on DIF
- attend ID & Discovery meetings of DIF!
- Next Meeting May 11, 2020 11 a.m. PDT
- https://zoom.us/j/801382311

- In process to add DIF slack channel for KERI
- In meantime can reach on this slack channel  keriworld.slack.com
- 
- DIF ID and Discovery Agenda
    - https://docs.google.com/document/d/1gV99R7G2bUlRerxznyd0ucwWPfc1v2F2gQVaTawIPYI/edit#

## Old Notes
KERI JS Scope

Event Streaming
Microservices Architecture
Serverless compatible

Each role is a different microservice

Common is core KERI engine

Database LMDB


Start with online case pairwise case

Controller and Validator



## Events

### Inception

![](https://i.imgur.com/foTO32t.png)


```json
{
  "version": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 0,
  "ilk": "icp",
  "digest": "EGEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "next": "EWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
  "tally": 2,
  "witnesses": 
  [
    "AVrTkep6H-Qt27fThWoNZsa884HA8tr54sHON1vWl6FE",
    "AHON1vWl6FEQt27fThWoNZsa88VrTkep6H-4HA8tr54s",
    "AThWoNZsa88VrTkeQt27fp6H-4HA8tr54sHON1vWl6FE",
  ],
  "data": [],
  "signatures": [0,1]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AHot0pmdWAcgTo5sKFFgf8i0tDq8XGizaCgAeYbsD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```

### Rotation

![](https://i.imgur.com/X38o6Kv.png)


```json
{
  "version": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 1,
  "ilk": "rot",
  "digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw=",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "next": "EWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
  "tally": 2,
  "prune": 
  [
    "AVrTkep6H-Qt27fThWoNZsa884HA8tr54sHON1vWl6FE",
  ],
  "graft": 
  [
    "AHA8tr54sHON1vWl6FEVrTkep6H-Qt27fThWoNZsa884",
  ],
  "seals": [],
  "signatures": [0,2]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AKFFgf8i0tDq8XGizaCgAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```

### Interaction

![](https://i.imgur.com/vr7oyg5.png)


```json
{
  "verison": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 2,
  "ilk": "ixn",
  "digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw=",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "seals": [],
  "signatures": [0,2]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AKFFgf8i0tDq8XGizaCgAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```

### Seals


```json

"seals":
[
  [
    ["prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148"],
    [ "sn": 0],
    ["digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw"]
  ]
  ,
  [
    ["prefix", "AY25YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn139"],
    ["sn", 0],
    ["digest", "GFj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw"]
  ]
]
```

### Delegation

![](https://i.imgur.com/QzBtfnb.png)



#### Delegating Interaction

![](https://i.imgur.com/bNrHhsu.png)

```json
{
  "verison": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 2,
  "ilk": "ixn",
  "digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw=",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "seals": 
  [ 
    [
      ["prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148"],
      [ "sn": 0],
      ["digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw"]
    ]
  ],
  "signatures": [0,2]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AKFFgf8i0tDq8XGizaCgAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```

#### Delegating Rotation



![](https://i.imgur.com/mipTTUr.png)


```json
{
  "version": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 1,
  "ilk": "rot",
  "digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw=",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "next": "EWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
  "tally": 2,
  "prune": 
  [
    "AVrTkep6H-Qt27fThWoNZsa884HA8tr54sHON1vWl6FE",
  ],
  "graft": 
  [
    "AHA8tr54sHON1vWl6FEVrTkep6H-Qt27fThWoNZsa884",
  ],
  "seals":
  [
    [
      ["prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148"],
      [ "sn": 0],
      ["digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw"]
    ]  
  ],
  "signatures": [0,2]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AKFFgf8i0tDq8XGizaCgAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```



### Delegated Inception

![](https://i.imgur.com/EaKLgLW.png)

![](https://i.imgur.com/JiAyeH5.png)


```json
{
  "version": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 0,
  "ilk": "dip",
  "digest": "EGEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "next": "EWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
  "tally": 2,
  "witnesses": 
  [
    "AVrTkep6H-Qt27fThWoNZsa884HA8tr54sHON1vWl6FE",
    "AHON1vWl6FEQt27fThWoNZsa88VrTkep6H-4HA8tr54s",
    "AThWoNZsa88VrTkeQt27fp6H-4HA8tr54sHON1vWl6FE",
  ],
  "seal": 
  [
    ["prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148"],
    [ "sn": 0],
    ["digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw"]
  ],
  "signatures": [0,1]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AHot0pmdWAcgTo5sKFFgf8i0tDq8XGizaCgAeYbsD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```

### Delegated Rotation

![](https://i.imgur.com/3T4h8Pu.png)

![](https://i.imgur.com/81nzRTt.png)


```json
{
  "version": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 1,
  "ilk": "rot",
  "digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw=",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "next": "EWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
  "tally": 2,
  "prune": 
  [
    "AVrTkep6H-Qt27fThWoNZsa884HA8tr54sHON1vWl6FE",
  ],
  "graft": 
  [
    "AHA8tr54sHON1vWl6FEVrTkep6H-Qt27fThWoNZsa884",
  ],
 "seal": 
  [
    ["prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148"],
    [ "sn": 0],
    ["digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw"]
  ],
  "signatures": [0,2]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AKFFgf8i0tDq8XGizaCgAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```


### Delegated Interaction


![](https://i.imgur.com/U3uqrZh.png)


```json
{
  "verison": "KERI 0.0.1",
  "prefix": "AXq5YqaL6L48pf0fu7IUhL0JRaU2_RxFP0AL43wYn148",
  "sn": 2,
  "ilk": "ixn",
  "digest": "GEj6YfRWmGViKAesa08UkNWukUkPGsdFPPboBAsjRBw=",
  "threshold": 2,
  "signers": 
  [
    "AWoNZsa88VrTkep6HQt27fTh-4HA8tr54sHON1vWl6FE",
    "A8tr54sHON1vWVrTkep6H-4HAl6FEQt27fThWoNZsa88",
    "AVrTkep6HHA8tr54sHON1Qt27fThWoNZsa88-4vWl6FE"
  ],
  "seals": [],
  "signatures": [0,2]
}
\r\n\r\n
"0AAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8KFFgf8i0tDq8XGizaCg"
\r\n\r\n
"0AKFFgf8i0tDq8XGizaCgAeYbsHot0pmdWAcgTo5sD8iAuSQAfnH5U6wiIGpVNJQQoYKBYrPPxAoIc1i5SHCIDS8"
```