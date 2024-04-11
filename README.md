The DID ITN Method Specification 1.0
==================

**Specification Status**: Working Draft

**Latest Draft:** [ITN DID Specification](https://github.com/itn-trust/itn-did-spec)

**Draft Created:** April 10, 2024

**Latest Update:** April 10, 2024

**Contributors & Editors:**

1. [Andreas Freund](https://github.com/Therecanbeonlyone1969)
2. [Dmitry Zotov](https://github.com/AlexMesser)
3. [Parth Bhatt](https://github.com/bparth24)
4. [Umed Khudoiberdiev](https://github.com/pleerock)


## Abstract

Integrated Trust Network (ITN) is a community-built and operated network. The objective is to build a Federated Trust Layer (Core Services) and Business Automation Interoperability Infrastructure (User Agents) for ITN. The ITN Identifier is specified in accordance with the [W3C DID Core specification](https://www.w3.org/TR/did-core/).

ITN Identifier = ITN DID

The syntax of the identifier and accompanying data model used by the protocol is conformant with the [DID-CORE](https://www.w3.org/TR/did-core/) specification and shall be registered with the [DID-SPEC-REGISTRIES](https://www.w3.org/TR/did-spec-registries/).

For a detailed read on DID and other DID method specifications, one can refer [WebOfTrustInfo - DID Primer](https://github.com/WebOfTrustInfo/rwot5-boston/blob/master/topics-and-advance-readings/did-primer.md)

## Architecture Overview

ITN Agent is an implementation of the [Aries Agent RFC](https://github.com/hyperledger/aries-rfcs/blob/main/concepts/0004-agents/README.md)
with a flexible plugin architecture.

Depending on which plugins and [DIDComm protocols](https://didcomm.org/search/?page=1) you use, your instance of ITN Agent can
perform a variety of roles:

* Create and manage cryptographic keys
* Create and manage Decentralized Identifiers
* Issue and verify Verifiable Credentials (VCs) and Presentations (VPs)
* Communicate with other Agents over [DIDComm](https://identity.foundation/didcomm-messaging/spec/#discover-features-protocol-20)

Agent provides a common interface for plugins to expand its functionality. It is designed to be modular and extensible,
so you can add new protocols, transports, and managers to fit your needs.

The agent was written in [TypeScript](https://www.typescriptlang.org/) and runs natively in Node.js, Browsers, and React Native.

## ITN SDK

![ITN_SDK](https://github.com/itn-trust/itn-did-spec/assets/18353464/6135b073-c085-41fd-8ea4-f80e64e0485d)

## Conformance

The keywords MAY, MUST, MUST NOT, RECOMMENDED, SHOULD, and SHOULD NOT in this document are to be interpreted... TBD `Not sure if required`

## Terminology

1. ITN - Integrated Trust Network
2. EDV - Encrypted Data Vault 

## DID ITN Method Specification

### Format

The ITN DID is derived as the sha3-256 hash of a random salt of sufficient entropy.

The format of ITN DID conform to the [W3C DID Core specification](https://www.w3.org/TR/did-core/). It consists of `did:itn` prefix followed by `sha3-256(random salt)`. The W3C DID Core specification does not specify how a DID is generated, and leaves it up to the implementation provided it ensures uniqueness to a high degree of confidence.

**Note:** Format section ensures the ITN DID Format complies with [W3C DID Method Syntax](https://w3c.github.io/did-core/#method-syntax)

### Operations

Identity Protocol provides methods to manage DIDs.

#### Usage of Identity Protocols

```typescript
import { IdentityProtocol, Agent } from "@itn-trust/agent"

const identityProtocol = new IdentityProtocol({
  "create-did": async (message: CreateDIDMessage, next) => {
    // handle CreateDIDMessage
  },
  "create-did-response": async (message: CreateDIDResponseMessage, next) => {
    // handle CreateDIDResponseMessage
  },
  "update-did": async (message: UpdateDIDMessage, next) => {
    // handle UpdateDIDMessage
  },
  "update-did-response": async (message: UpdateDIDResponseMessage, next) => {
    // handle UpdateDIDResponseMessage
  },
  "revoke-did": async (message: RevokeDIDMessage, next) => {
    // handle RevokeDIDMessage
  },
  "revoke-did-response": async (message: RevokeDIDResponseMessage, next) => {
    // handle RevokeDIDResponseMessage
  },
  "recover-did": async (message: RecoverDIDMessage, next) => {
    // handle RecoverDIDMessage
  },
  "recover-did-response": async (message: RecoverDIDResponseMessage, next) => {
    // handle RecoverDIDResponseMessage
  },
})

const agent = new Agent({
  // ...
  protocols: [identityProtocol],
})
```

**Refer** [ITN Identity Protocol](https://github.com/itn-trust/itn/tree/master/packages/sdk/agent/src/protocol/itn.mobi/identity/1.0) for technical details. `DELETE LATER BEFORE CREATING PR`

---

#### Create

Description: Based on the DID data schema context file and DID method an ITN process anchors a DID and its DID document and delivers it to the requestor.
Constraints: Compliance with the DID data schema and DID method requirements. Can only be invoked by the DID controller(s).


##### `create()` API

```ts
create({
  creator: DIDString | DIDDocument,
  newDID?: {
    didDoc: DIDDocument,
    recoveryKey: Ed25519VerificationKey2020,
  }
}): Promise<DIDDocument>
```
* `creator` - creator of the new DID, can be a DID string or DID document.
* `newDID.didDoc` - (optional) a pre-created DID Document.
* `newDID.recoveryKey` - (optional) a pre-created DID Document's recovery key.

Sends `CreateDIDMessage` to `creator` and receives `CreateDIDResponseMessage` message in `create-did-response` handler.

**example:**

```typescript
await agent.protocol(IdentityProtocol).create({ creator: "did:itn:TW7PbLSe2Ws8FCx675oFUU" })
```

---

#### Resolve

Description: Based on the DID data schema context file and DID method an ITN process delivers a DID document in accordance with the W3C DID standard.
Constraints: Compliance with the DID data schema context file and DID method requirements. Any entity should be able to invoke the process.


#### Update

Description: Based on the DID data schema context file and DID method an ITN process creates an updated DID document in accordance with the W3C DID standard.
Constraints: Compliance with the DID data schema context file and DID method requirements. Can only be called by the DID controller(s).


##### `update()` API

```ts
update({
  receiver: DIDString | DIDDocument,
  oldDIDDoc: DIDDocument,
  newDIDDoc: DIDDocument,
}):Promise<void>
```
* `receiver` - receiver of the update, can be a DID string or a DID document.
* `oldDIDDoc` - old DID Document to be updated
* `newDIDDoc` - new DID document containing the updates.

Sends `UpdateDIDMessage` to `receiver` and receives `UpdateDIDResponseMessage` message in `update-did-response` handler.

**example:**

```typescript

const oldDIDDoc = {
  "@context": ["https://www.w3.org/ns/did/v1"],
  id: "did:itn:8FcBrpSd5PTafaAzThsPbF",
  assertionMethod: [
    "did:itn:8FcBrpSd5PTafaAzThsPbF#z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
  ],
  authentication: [
    "did:itn:8FcBrpSd5PTafaAzThsPbF#z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
  ],
  keyAgreement: [
    {
      id: "did:itn:8FcBrpSd5PTafaAzThsPbF#z6LScBaA4yAZowVqszMCBd5zscDs6Vk4npARXGBw29ewngNX",
      type: "X25519KeyAgreementKey2020",
      controller: "did:itn:8FcBrpSd5PTafaAzThsPbF",
      publicKeyMultibase:
        "z6LScBaA4yAZowVqszMCBd5zscDs6Vk4npARXGBw29ewngNX",
    },
  ],
  verificationMethod: [
    {
      id: "did:itn:8FcBrpSd5PTafaAzThsPbF#z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
      type: "Ed25519VerificationKey2020",
      controller: "did:itn:8FcBrpSd5PTafaAzThsPbF",
      publicKeyMultibase:
        "z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
    },
    {
      id: "did:itn:test#key-1",
      type: "Ed25519VerificationKey2018",
      controller: "did:itn:test",
      publicKeyBase58: "FgF1dWCiADqWr97DUxsq6Zf1ZrzJ",
    },
  ],
  controller: "did:itn:Kmp7sVNtMrXZyiTKwgVWJK",
}

const newDIDDoc = {
  "@context": ["https://www.w3.org/ns/did/v1"],
  id: "did:itn:8FcBrpSd5PTafaAzThsPbF",
  assertionMethod: [
    "did:itn:8FcBrpSd5PTafaAzThsPbF#z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
  ],
  authentication: [
    "did:itn:8FcBrpSd5PTafaAzThsPbF#z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
  ],
  keyAgreement: [
    {
      id: "did:itn:8FcBrpSd5PTafaAzThsPbF#z6LScBaA4yAZowVqszMCBd5zscDs6Vk4npARXGBw29ewngNX",
      type: "X25519KeyAgreementKey2020",
      controller: "did:itn:8FcBrpSd5PTafaAzThsPbF",
      publicKeyMultibase:
        "z6LScBaA4yAZowVqszMCBd5zscDs6Vk4npARXGBw29ewngNX",
    },
  ],
  verificationMethod: [
    {
      id: "did:itn:8FcBrpSd5PTafaAzThsPbF#z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
      type: "Ed25519VerificationKey2020",
      controller: "did:itn:8FcBrpSd5PTafaAzThsPbF",
      publicKeyMultibase:
        "z6MkvXb9Cv5iaMEtn7PtJA5bg7Fnz3nT3AVU59ViAUJLtRrH",
    },
    {
      id: "did:itn:test#key-1",
      type: "Ed25519VerificationKey2018",
      controller: "did:itn:test",
      publicKeyBase58: "FgF1dWCiADqWr97DUxsq6Zf1ZrzJ",
    },
  ],
  controller: "did:itn:Kmp7sVNtMrXZyiTKwgVWJK",
  service: [
    {
      id: "did:itn:info",
      type: "Information",
      serviceEndpoint: "https://example.com/info",
    },
  ],
  alsoKnownAs: ["https://itn.example/"],
}

await agent.protocol(IdentityProtocol).update({ receiver: "did:itn:TW7PbLSe2Ws8FCx675oFUU", oldDIDDoc, newDIDDoc })
```

---


#### Revoke

Description: Based on the DID data schema context file and DID method an ITN process revokes an existing DID and its DID document in accordance with the W3C DID standard.
Constraints: Compliance with the DID data schema context file and DID method. Can only be invoked by the DID controller(s).

##### `revoke()` API

```ts
revoke({ did: DIDString, receiver: DIDString | DIDDocument }):Promise<void>
```
* `did` - DID to be revoked.
* `receiver` - receiver of the revocation, can be a DID string or a DID document.

Sends `RevokeDIDMessage` to `receiver` and receive `RevokeDIDResponseMessage` in `revoke-did-response` handler.

**example:**

```typescript

await agent
  .protocol(IdentityProtocol)
  .revoke({ did: "did:itn:8FcBrpSd5PTafaAzThsPbF", receiver: "did:itn:TW7PbLSe2Ws8FCx675oFUU" })
```

---


#### Recover

Description: Based on the DID data schema context file and DID method, an ITN process recovers a DID and its DID document in accordance with the W3C DID standard.
Constraints: Compliance with the DID data schema context file and DID method requirements. Can only be invoked by the DID controller(s).

##### `recover()` API

```ts
recover({ did: DIDString, receiver: DIDString | DIDDocument }): Promise<void>
```
* `did` - DID to be recovered.
* `receiver` - receiver of the recovery, can be a DID string or a DID document.

Sends `RecoverDIDMessage` to `receiver` and receive `RecoverDIDResponseMessage` in `recover-did-response` handler.

**example:**

```typescript

await agent.protocol(IdentityProtocol).recover({ did: "did:itn:8FcBrpSd5PTafaAzThsPbF", receiver: "did:itn:TW7PbLSe2Ws8FCx675oFUU" })
```

---

#### Deactivate

The ITN DID Method does not have a Deactivate operation.

**Note:** Operations section ensures the ITN DID Method Operations complies with [W3C DID Method Operations](https://w3c.github.io/did-core/#method-operations)

## Security Considerations

For all `did:itn` DIDs, the initial asset creation, and subsequent updates are executed using `Ed25519` keys, which are widely recognized as a robust and secure cryptographic mechanism. `Confirm With Umed & Andreas - if the keys are different. DELETE THIS COMMENT LATER.`

**Note:** Security Considerations section ensures the ITN DID Method Security considerations comply with [W3C DID Method Security Requirements](https://w3c.github.io/did-core/#security-requirements)

## Privacy Considerations

Text - TBD

**Note:** Privacy Considerations section ensures the ITN DID Method Privacy considerations complies with [W3C DID Method Privacy Requirements](https://w3c.github.io/did-core/#privacy-requirements)

## Apendix

ITN SDK has crypto-ld-suite package that provides a set of functions to generate crypto keys for use with [@digitalbazaar/crypto-ld](https://github.com/digitalbazaar/crypto-ld) library, a set of functions to create and verify signatures for use with [@digitalbazaar/jsonld-signatures](https://github.com/digitalbazaar/jsonld-signatures) library and a set of utility functions for use with crypto keys.

Cryptographic Key Types supported

    Ed25519VerificationKey2018
    Ed25519VerificationKey2020
    X25519KeyAgreementKey2019
    X25519KeyAgreementKey2020
    EcdsaSecp256k1VerificationKey2019
    EcdsaSecp256r1VerificationKey2020
    EcdsaSecp384r1VerificationKey2020
    EcdsaSecp521r1VerificationKey2020
    RsaVerificationKey2018
    Bls12381G1Key2020
    Bls12381G2Key2020

Supported curves are:

    Ed25519
    X25519
    RSA
    P-256
    P-384
    P-521
    Bls12381G1
    Bls12381G2
    secp256k1

Refer: [crypto-ld-suite](https://github.com/itn-trust/itn/tree/master/packages/sdk/crypto-ld-suite) `Not sure where to add and how to explain. Need some guidance.`
____________

## Message Encryptor

Provides a set of functions to encrypt and decrypt DIDComm messages. Message Encryptor supports two forms of message
encryption: Authenticated Sender Encryption (`authcrypt`) and Anonymous Sender Encryption (`anoncrypt`).
Both forms are encrypted to the recipient DID. Only `authcrypt` provides direct assurances of who the sender is.

#### Message Encryptor supports the following curves for the keys involved in key agreement:

| Curve  | Description                                                                                                                           |
|--------|---------------------------------------------------------------------------------------------------------------------------------------|
| X25519 | The underlying curve is actually Curve25519, however when used in the context of Diffie-Hellman<br/> the identifier of X25519 is used |
| P-256  | NIST defined P-256 elliptic curve.                                                                                                    |
| P-384  | NIST defined P-384 elliptic curve.                                                                                                    |
| P-521  | NIST defined P-521 elliptic curve.                                                                                                    |

##
#### The following `AEAD` algorithms are supported for content encryption of the message:

| Algorithm     | Description                                 |
|---------------|---------------------------------------------|
| A256CBC-HS512 | AES256-CBC + HMAC-SHA512 with a 512 bit key |
| A256GCM       | AES256-GCM with a 256 bit key               |
| XC20P         | XChaCha20Poly1305 with a 256 bit key        |

##
#### The following Key Wrapping Algorithms are supported to wrap shared secret key:
| Algorithm       | Curve (epk crv)  | key type (epk kty) |
|-----------------|------------------|--------------------|
| ECDH-ES+A256KW  | P-256            | EC                 |
| ECDH-ES+A256KW  | P-384            | EC                 |
| ECDH-ES+A256KW  | P-521            | EC                 |
| ECDH-ES+A256KW  | X25519           | OKP                |
| ECDH-1PU+A256KW | P-256            | EC                 |
| ECDH-1PU+A256KW | P-384            | EC                 |
| ECDH-1PU+A256KW | P-521            | EC                 |
| ECDH-1PU+A256KW | X25519           | OKP                |

Refer: [Agent Services](https://github.com/itn-trust/itn/blob/master/packages/sdk/agent/src/service/README.md#agent-services) `Not sure where to add and how to explain. Need some guidance.`

## References

[1] W3C Decentralized Identifiers (DIDs) v1.0, https://w3c.github.io/did-core/
