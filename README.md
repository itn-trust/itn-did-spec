The DID ITN Method Specification 1.0
==================

**Specification Status**: Working Draft

**Latest Draft:** [ITN DID Specification](https://github.com/itn-trust/itn-did-spec)

**Draft Created:** April 10, 2024

**Latest Update:** April 15, 2024

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

The ITN is a permissioned network of nodes operated by ITN Node Operators.

Each ITN Node is comprised of the following elements depicted in the figure below:
- ITN Agent for Core Services ~ (former name used - Self Sovereign Digital Twin (SSDT) of the Core Services OR Core Services SSDT)
- DLT Gateway: A NodeJS application integrating with the Core Services SSDT, Apache Kafka, and the different DLT networks of the ITN. In the next phase of implementation, the Apache Kafka instances will be connected via message replication to obtain a holistic picture of messages across the network.
- DID Resolver: The DID resolver utilizes the Decentralized Identity Foundation DID resolver and is integrated with the Core Services SSDT.
- Content Addressable Storage Network: Currently utilizing an append-only implementation of a Postgres DB with content-addressable hashes and replication between ITN Nodes. There is an implementation of peer-to-peer replication using the IPFS Cluster.
- DLT Networks: It is comprised of a Hyperledger Fabric network and Arbitrum One EVM equivalent network (with more to follow, in particular, based on Integrated Trust Network (ITN) expansion).

All applications external to the ITN and wishing to utilize the ITN Core Services must implement an ITN Agent as their abstraction layer through the ITN SDK in the [ITN GitHub repository](https://github.com/itn-trust/itn). An ITN Agent represents a user’s capabilities on ITN and serves as an abstraction and integration layer between ITN and other applications/networks.

![ITN Architecture Overview](./docs/images/ITN%20Architecture%20Overview.jpg)


The ITN Agent utilized in each ITN node is implemented in NodeJS and is comprised of the following elements:

- API Endpoint Service for both REST APIs and [Decentralized Identity Foundation (DIF) DIDcomm](https://identity.foundation/didcomm-messaging/spec/) messaging protocol for integration with ITN Core Services, other Agents, and Legacy Applications via REST APIs
- Authentication service utilizing [OAuth2 for REST APIs](https://oauth.net/2/) and [DIF DID AuthN](https://identity.foundation/working-groups/authentication.html) for DIF DIDcomm messaging
- An implementation of the [W3C Universal Wallet](https://w3c-ccg.github.io/universal-wallet-interop-spec/) for key and document management
- An implementation of the W3C Verifiable Credential Issuance and Verification standard both as REST APIs and as DIDcomm endpoints
- Implementation of ITN Data Sharing APIs both as REST APIs and as DIDcomm endpoints
- An implementation of ITN Core Services functionality via DIF DIDcomm
- An implementation of the [W3C/DIF Encrypted Data Vault](https://identity.foundation/confidential-storage/) for document storage currently utilizing CouchDB with Leader-Leader replication

Integration of an ITN Agent in any application is achieved through the ITN SDK.

![ITN Agent](./docs/images/ITN%20Agent.jpg)


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

The ITN SDK has an Identity Protocol provides methods to manage DIDs.
The ITN SDK has a DID Document Manager provide a set of functions to manage DID Documents.

`Conirm with Umed & Andreas - here I am only providing necessary information related to DIDs & DID Document. Is that okay?`

## Conformance

The keywords MAY, MUST, MUST NOT, RECOMMENDED, SHOULD, and SHOULD NOT in this document are to be interpreted... TBD `Not sure if required`

## Terminology

1. ITN - Integrated Trust Network
2. EDV - Encrypted Data Vault 

## DID ITN Method Specification

### Format

The ITN DID is derived as follow:

- A random 32 byte string is generated
- The seed is hashed using sha512
- An Ed25519 key pair is generated from the hash. The key pair will be used as the recovery key pair
- The uuid of the did:itn is the base58 encoded value of public keys hexadecimal value

The format of ITN DID conform to the [W3C DID Core specification](https://www.w3.org/TR/did-core/). The W3C DID Core specification does not specify how a DID is generated, and leaves it up to the implementation provided it ensures uniqueness to a high degree of confidence.

**Note:** Format section ensures the ITN DID Format complies with [W3C DID Method Syntax](https://w3c.github.io/did-core/#method-syntax)

### Operations

Identity Protocol provides methods to manage DIDs.

#### Create

Description: Based on the DID data schema context file and DID method an ITN process anchors a DID and its DID document and delivers it to the requestor.
Constraints: Can only be invoked by the DID controller(s).

Requirements:

- `creator` input parameter can be a DID String or a DID document.
- The provided DID MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.

`Note: Question to Umed -- as per below comments - if its just DID string - how & when DID Document is created then??`

---

#### Resolve

Description: Based on the DID data schema context file and DID method an ITN process delivers a DID document in accordance with the W3C DID standard.
Constraints: Any entity should be able to invoke the process.

Requirements:

- A DID string MUST be provided as an input parameter.
- The provided DID MUST be compliant with the did:itn method.

**example:**

```typescript
await agent.resolveDIDDoc("did:itn:NL28Bubaa8xUmhJAARNkNS")
```

Response to above code will be a DID Document as below:

```json
{
  "didDocument": {
    "@context": [
      "https://www.w3.org/ns/did/v1"
    ],
    "assertionMethod": [
      "did:itn:NL28Bubaa8xUmhJAARNkNS#z6MkiYmb7ncfDAAhcxtAAGsm1oySq4KYJtsi2wHaoKAnmVXC"
    ],
    "authentication": [
      "did:itn:NL28Bubaa8xUmhJAARNkNS#z6MkiYmb7ncfDAAhcxtAAGsm1oySq4KYJtsi2wHaoKAnmVXC"
    ],
    "id": "did:itn:NL28Bubaa8xUmhJAARNkNS",
    "keyAgreement": [
      {
        "id": "did:itn:NL28Bubaa8xUmhJAARNkNS#z6LSd76Tk2UJ3RVxcTjYjZ8TfvHMQgZG5HmbKPmCidPXyTGZ",
        "type": "X25519KeyAgreementKey2020",
        "controller": "did:itn:NL28Bubaa8xUmhJAARNkNS",
        "publicKeyMultibase": "z6LSd76Tk2UJ3RVxcTjYjZ8TfvHMQgZG5HmbKPmCidPXyTGZ"
      }
    ],
    "verificationMethod": [
      {
        "id": "did:itn:NL28Bubaa8xUmhJAARNkNS#z6MkiYmb7ncfDAAhcxtAAGsm1oySq4KYJtsi2wHaoKAnmVXC",
        "type": "Ed25519VerificationKey2020",
        "controller": "did:itn:NL28Bubaa8xUmhJAARNkNS",
        "publicKeyMultibase": "z6MkiYmb7ncfDAAhcxtAAGsm1oySq4KYJtsi2wHaoKAnmVXC"
      }
    ]
  },
  "didDocumentMetadata": {
    "created": "2023-11-10T15:20:59Z",
    "updated": "2023-11-10T15:20:59Z",
    "deactivated": false,
    "recoveryCommitment": "99cdefca761b58664641d3f0b89b4f1e04ad833b7a24a726e3424bc6c17f39a0"
  },
  "didResolutionMetadata": { }
}
```

---

#### Update

Description: Based on the DID data schema context file and DID method an ITN process creates an updated DID document in accordance with the W3C DID standard.
Constraints: Can only be called by the DID controller(s).

Requirements:

- `receiver` input parameter can be a DID string or a DID Document.
- The provided DID string MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.

---


#### Revoke

Description: Based on the DID data schema context file and DID method an ITN process revokes an existing DID and its DID document in accordance with the W3C DID standard.
Constraints: Can only be invoked by the DID controller(s).

Requirements:

- `did` input parameter MUST be a DID string that has to be revoked.
- `receiver` input parameter can be a DID string or a DID Document.
- The provided DID string MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.


---


#### Recover

Description: Based on the DID data schema context file and DID method, an ITN process recovers a DID and its DID document in accordance with the W3C DID standard.
Constraints: Can only be invoked by the DID controller(s).

Requirements:

- `did` input parameter MUST be a DID string that has to be recovered.
- `receiver` input parameter can be a DID string or a DID Document.
- The provided DID string MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.

---

#### Deactivate

The `revoke` operation also deactivate the DID.

**Note:** Operations section ensures the ITN DID Method Operations complies with [W3C DID Method Operations](https://w3c.github.io/did-core/#method-operations)



## Security Considerations

For all `did:itn` DIDs, the initial asset creation, and subsequent updates are executed using `Ed25519` keys, which are widely recognized as a robust and secure cryptographic mechanism. `Confirm With Umed & Andreas - if the keys are different. DELETE THIS COMMENT LATER.`

**Note:** Security Considerations section ensures the ITN DID Method Security considerations comply with [W3C DID Method Security Requirements](https://w3c.github.io/did-core/#security-requirements)

## Privacy Considerations

Text - TBD

**Note:** Privacy Considerations section ensures the ITN DID Method Privacy considerations complies with [W3C DID Method Privacy Requirements](https://w3c.github.io/did-core/#privacy-requirements)

## Appendix

### DID Document Manager

Provides a set of functions to manage DID Documents.

#### `getDIDDoc()` API

```ts
getDIDDoc(did: DIDString): Promise<DIDDocument | null>
```
* `did` - DID Document identifier

Retrieves the DID document for the specified DID.

**example:**

```typescript
const didDoc = await agent.managers.didDoc.getDIDDoc("did:itn:RGNQknTkhX6eiFaw38DrLP")

expect(didDoc).toBe({
  "@context": ["https://www.w3.org/ns/did/v1"],
  id: "did:itn:RGNQknTkhX6eiFaw38DrLP",
  assertionMethod: [
    "did:itn:RGNQknTkhX6eiFaw38DrLP#z6MkkFbwWFtiXE8ioC293Fzn2hXDR5jpMXDB9iWFwz5t8WF9",
  ],
  authentication: [
    "did:itn:RGNQknTkhX6eiFaw38DrLP#z6MkkFbwWFtiXE8ioC293Fzn2hXDR5jpMXDB9iWFwz5t8WF9",
  ],
  keyAgreement: [
    {
      id: "did:itn:RGNQknTkhX6eiFaw38DrLP#z6LSf7iSoxWXoRBjEHR3ZSHePgooDjRVZhAih59QUaHchPNB",
      type: "X25519KeyAgreementKey2020",
      controller: "did:itn:RGNQknTkhX6eiFaw38DrLP",
      publicKeyMultibase: "z6LSf7iSoxWXoRBjEHR3ZSHePgooDjRVZhAih59QUaHchPNB",
    },
  ],
  verificationMethod: [
    {
      id: "did:itn:RGNQknTkhX6eiFaw38DrLP#z6MkkFbwWFtiXE8ioC293Fzn2hXDR5jpMXDB9iWFwz5t8WF9",
      type: "Ed25519VerificationKey2020",
      controller: "did:itn:RGNQknTkhX6eiFaw38DrLP",
      publicKeyMultibase: "z6MkkFbwWFtiXE8ioC293Fzn2hXDR5jpMXDB9iWFwz5t8WF9",
    },
  ],
})
```

#### `saveDIDDoc()` API

```ts
saveDIDDoc(args?: {
  did?: DIDString
  controller?: DIDDocument
  operationId?: UUIDString
  recoveryKey?: Ed25519VerificationKey2020
  didDocument?: Partial<DIDDocument>
  metadata?: {
    name?: string
    description?: string
    tags?: string[]
  }
}): Promise<{
  recoveryKey: Ed25519VerificationKey2020
  didDoc: DIDDocument
}>
```
* `args` - (optional) arguments for saving the DID document

Saves the DID document along with the necessary keys and metadata into the wallet.
If `operationId` provided, saves as a wallet operation until `wallet.commitChanges()` or `wallet.rollbackChanges()` is called.

**example:**

```typescript
const { recoveryKey, didDoc } = await agent.managers.didDoc.createDID()

expect(recoveryKey).toBe({
  "@context": ["https://w3id.org/security/suites/ed25519-2020/v1"],
  id: "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6Mksmhto6EJEHQ97ewZMv6hAcSEvWTw3dBLfu2vDax94qxu",
  type: "Ed25519VerificationKey2020",
  controller: "did:itn:RSF6nsbNFXW1Rpny3pKUuu",
  publicKeyMultibase: "z6Mksmhto6EJEHQ97ewZMv6hAcSEvWTw3dBLfu2vDax94qxu",
  privateKeyMultibase: "z3u2i7PnbgcQvCxbBbmEYA4sfXmGhswEiYB5LHUAWsgFXaXW",
  name: "Recovery Key",
  description: "Used to recover identity.",
  tags: [],
})


expect(didDoc).toBe({
  "@context": ["https://www.w3.org/ns/did/v1"],
  id: "did:itn:RSF6nsbNFXW1Rpny3pKUuu",
  assertionMethod: [
    "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
  ],
  authentication: [
    "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
  ],
  keyAgreement: [
    {
      id: "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6LSpUuyUHWwpjyK59xE9d5ZrtDqN6ya7RzBjyPxgsAT2VNL",
      type: "X25519KeyAgreementKey2020",
      controller: "did:itn:RSF6nsbNFXW1Rpny3pKUuu",
      publicKeyMultibase: "z6LSpUuyUHWwpjyK59xE9d5ZrtDqN6ya7RzBjyPxgsAT2VNL",
    },
  ],
  verificationMethod: [
    {
      id: "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
      type: "Ed25519VerificationKey2020",
      controller: "did:itn:RSF6nsbNFXW1Rpny3pKUuu",
      publicKeyMultibase: "z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
    },
  ],
})
```

---

#### `updateDIDDoc()` API

```ts
 updateDIDDoc(
    oldDIDDoc: DIDDocument,
    newDIDDoc: DIDDocument,
    operationId?: UUIDString,
  ): Promise<void>
```
* `oldDIDDoc` - old DID Document
* `newDIDDoc` - new DID Document
* `operationId` - (optional) if provided, saves DID Document update as a wallet operation until `wallet.commitChanges()` or `wallet.rollbackChanges()` is called.

Updates the DID Document.

**example:**

```typescript
const oldDIDDoc = {
  "@context": ["https://www.w3.org/ns/did/v1"],
  id: "did:itn:X9ns1DteHSXCL8AjgCvDhF",
  assertionMethod: [
    "did:itn:X9ns1DteHSXCL8AjgCvDhF#z6Mkn1HjxP4C9sq6P6ruemWe8eQZa3jPLAADLJUYJnZ9g5xj",
  ],
  authentication: [
    "did:itn:X9ns1DteHSXCL8AjgCvDhF#z6Mkn1HjxP4C9sq6P6ruemWe8eQZa3jPLAADLJUYJnZ9g5xj",
  ],
  keyAgreement: [
    {
      id: "did:itn:X9ns1DteHSXCL8AjgCvDhF#z6LSenEkHyRX5sqDX7KsPFta9daxNNWNuNV9nZPHFLiF6343",
      type: "X25519KeyAgreementKey2020",
      controller: "did:itn:X9ns1DteHSXCL8AjgCvDhF",
      publicKeyMultibase: "z6LSenEkHyRX5sqDX7KsPFta9daxNNWNuNV9nZPHFLiF6343",
    },
  ],
  verificationMethod: [
    {
      id: "did:itn:X9ns1DteHSXCL8AjgCvDhF#z6Mkn1HjxP4C9sq6P6ruemWe8eQZa3jPLAADLJUYJnZ9g5xj",
      type: "Ed25519VerificationKey2020",
      controller: "did:itn:X9ns1DteHSXCL8AjgCvDhF",
      publicKeyMultibase: "z6Mkn1HjxP4C9sq6P6ruemWe8eQZa3jPLAADLJUYJnZ9g5xj",
    },
  ],
}

const newDIDDoc = {
  ...didDoc,
  service: [
    {
      id: "did:itn:info",
      type: "Information",
      serviceEndpoint: "https://example.com/info",
    },
  ],
}

await agent.managers.didDoc.updateDIDDoc(oldDIDDoc, newDIDDoc)
```

---

#### `revokeDID()` API

```ts
revokeDID(did: DIDString, operationId?: UUIDString): Promise<void> 
```
* `did` - DID to be revoked
* `operationId` - (optional) if provided, saves DID Document revoke as a wallet operation until "wallet.commitChanges()" or "wallet.rollbackChanges()" is called.

Revoke a DID by updating its associated DID Document.

**example:**

```typescript
await agent.managers.didDoc.revokeDID("did:itn:4wqjsfTA8jghZK9Xw6TQDL")
```

---

#### `recoverDID()` API

```ts
recoverDID(did: DIDString, operationId?: UUIDString): Promise<{
  recoveryKey: Ed25519VerificationKey2020
  didDocContent: DIDDocContent
}> 
```
* `did` - DID to be recovered
* `operationId` - (optional) if provided, saves DID Document revoke as a wallet operation until "wallet.commitChanges()" or "wallet.rollbackChanges()" is called.

Recovers a DID by creating a new DID document, signing and encryption keys, and updating the existing DID document content
in the wallet.


**example:**

```typescript
const {
  recoveryKey,
  didDocContent,
} = await wallet.didDocManager.recoverDID(
  "did:itn:Aw8tt7G1AhSCHfBrsuzrJG")

// new recovery key
expect(recoveryKey).toBe(
  {
    "@context": ["https://w3id.org/security/suites/ed25519-2020/v1"],
    id: "did:itn:Aw8tt7G1AhSCHfBrsuzrJG#z6MkjsGqthRZLsYW3EaYW63iRbXFeGJftFmsA3Z1uJziSPq7",
    type: "Ed25519VerificationKey2020",
    controller: "did:itn:Aw8tt7G1AhSCHfBrsuzrJG",
    publicKeyMultibase: "z6MkjsGqthRZLsYW3EaYW63iRbXFeGJftFmsA3Z1uJziSPq7",
    privateKeyMultibase: "z3u2W6p14p2mTmCCozMZqqdiSjYYtfFXfDjcutQDRTyDQjY7",
    name: "Recovery Key",
    description: "Used to recover identity.",
    tags: [],
  },
)

// new did document content
expect(didDocContent).toBe(
  {
    id: "63caefb9-1af2-48e9-9f22-9cf04ed7558e",
    type: "DIDDocument",
    body: {
      tags: [],
      didDocument: {
        "@context": [
          "https://www.w3.org/ns/did/v1",
        ],
        id: "did:itn:Aw8tt7G1AhSCHfBrsuzrJG",
        assertionMethod: [
          "did:itn:Aw8tt7G1AhSCHfBrsuzrJG#z6Mkuz76zmSj8Fb6PeE4AThcVTUeVADfBw4xQcGf6tYPx2SK",
        ],
        authentication: [
          "did:itn:Aw8tt7G1AhSCHfBrsuzrJG#z6Mkuz76zmSj8Fb6PeE4AThcVTUeVADfBw4xQcGf6tYPx2SK",
        ],
        keyAgreement: [
          {
            id: "did:itn:Aw8tt7G1AhSCHfBrsuzrJG#z6LSquqPkSUf9YHjoNYmuD6Jn5HxZihvXnXi9YrcJm42rMGT",
            type: "X25519KeyAgreementKey2020",
            controller: "did:itn:Aw8tt7G1AhSCHfBrsuzrJG",
            publicKeyMultibase: "z6LSquqPkSUf9YHjoNYmuD6Jn5HxZihvXnXi9YrcJm42rMGT",
          },
        ],
        verificationMethod: [
          {
            id: "did:itn:Aw8tt7G1AhSCHfBrsuzrJG#z6Mkuz76zmSj8Fb6PeE4AThcVTUeVADfBw4xQcGf6tYPx2SK",
            type: "Ed25519VerificationKey2020",
            controller: "did:itn:Aw8tt7G1AhSCHfBrsuzrJG",
            publicKeyMultibase: "z6Mkuz76zmSj8Fb6PeE4AThcVTUeVADfBw4xQcGf6tYPx2SK",
          },
        ],
        controller: "did:itn:RGNQknTkhX6eiFaw38DrLP",
      },
    },
    created: 1699006922350,
    updated: 1699006922350,
  },
)
```

---

#### `getDIDResolutionResultContent()` API

```ts
getDIDResolutionResultContent(did: DIDString): Promise<DIDResolutionContent | undefined>
```
* `did` - DID Document to retrieve the resolution result

Retrieves the content of a DID resolution result based on the provided DID.

**example:**

```typescript
const didResolutionContent = await agent.managers.didDoc.getDIDResolutionResultContent("did:itn:BRZ2CKoq2kV1WiuMYZcBwu")

expect(didResolutionContent).toBe({
  id: "f0967614-8bc2-4f7d-9dba-76ba18205113",
  type: "DIDResolutionResult",
  body: {
    "@context": ["https://w3id.org/did-resolution/v1"],
    tags: [],
    created: "2023-11-03T10:42:27.093Z",
    expires: "2023-11-04T10:42:27.093Z",
    didDocument: {
      "@context": ["https://www.w3.org/ns/did/v1"],
      id: "did:itn:B8G98VLFmv1o1h3CVF8fLQ",
      assertionMethod: ["did:itn:BRZ2CKoq2kV1WiuMYZcBwu#z6MkgG2CF1h4gcjpxX5wW1z55NzJ6DQMmJnVNcagb4kCggMM"],
      authentication: ["did:itn:BRZ2CKoq2kV1WiuMYZcBwu#z6MkgG2CF1h4gcjpxX5wW1z55NzJ6DQMmJnVNcagb4kCggMM"],
      keyAgreement: [{
        id: "did:itn:BRZ2CKoq2kV1WiuMYZcBwu#z6LSrGYukPKRADR2GZk25uAYwpZdF2qF75Xgm6dQS3gdEB1F",
        type: "X25519KeyAgreementKey2020",
        controller: "did:itn:BRZ2CKoq2kV1WiuMYZcBwu",
        publicKeyMultibase: "z6LSrGYukPKRADR2GZk25uAYwpZdF2qF75Xgm6dQS3gdEB1F",
      }],
      verificationMethod: [{
        id: "did:itn:BRZ2CKoq2kV1WiuMYZcBwu#z6MkgG2CF1h4gcjpxX5wW1z55NzJ6DQMmJnVNcagb4kCggMM",
        type: "Ed25519VerificationKey2020",
        controller: "did:itn:BRZ2CKoq2kV1WiuMYZcBwu",
        publicKeyMultibase: "z6MkgG2CF1h4gcjpxX5wW1z55NzJ6DQMmJnVNcagb4kCggMM",
      }],
    },
    didResolutionMetadata: {},
    didDocumentMetadata: {
      created: "2023-11-03T10:42:27.093Z",
    },
  },
  created: 1699008147093,
  updated: 1699008147093,
})
```

---

#### `saveDIDResolutionResult()` API

```ts
saveDIDResolutionResult(result: DIDResolutionResult): Promise<void>
```

* `result` - DID resolution result to be saved

Saves the DID resolution result to the wallet.

**example:**

```typescript
 const didResolutionResult = {
  didDocument: {
    "@context": ["https://www.w3.org/ns/did/v1"],
    id: "did:itn:RSF6nsbNFXW1Rpny3pKUuu",
    assertionMethod: [
      "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
    ],
    authentication: [
      "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
    ],
    keyAgreement: [
      {
        id: "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6LSpUuyUHWwpjyK59xE9d5ZrtDqN6ya7RzBjyPxgsAT2VNL",
        type: "X25519KeyAgreementKey2020",
        controller: "did:itn:RSF6nsbNFXW1Rpny3pKUuu",
        publicKeyMultibase: "z6LSpUuyUHWwpjyK59xE9d5ZrtDqN6ya7RzBjyPxgsAT2VNL",
      },
    ],
    verificationMethod: [
      {
        id: "did:itn:RSF6nsbNFXW1Rpny3pKUuu#z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
        type: "Ed25519VerificationKey2020",
        controller: "did:itn:RSF6nsbNFXW1Rpny3pKUuu",
        publicKeyMultibase: "z6MkuFe4dc9njM5uBNQL2rhubLx5jZRjLP8ftuXwz45Mvg5F",
      },
    ],
  },
  contentType: undefined,
  created: new Date().toISOString(),
}

await agent.managers.didDoc.saveDIDResolutionResult(didResolutionResult)
```

---

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
