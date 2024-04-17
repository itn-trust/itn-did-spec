The DID ITN Method Specification 1.0
==================

**Specification Status**: Working Draft

**Latest Draft:** [ITN DID Specification](https://github.com/itn-trust/itn-did-spec)

**Draft Created:** April 10, 2024

**Latest Update:** April 16, 2024

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

All applications external to the ITN and wishing to utilize the ITN Core Services must implement an ITN Agent as their abstraction layer. An ITN Agent represents a user’s capabilities on ITN and serves as an abstraction and integration layer between ITN and other applications/networks.

![ITN Architecture Overview](./docs/images/ITN%20Architecture%20Overview.jpg)

The ITN Agent utilized in each ITN node is comprised of the following elements:

- API Endpoint Service for both REST APIs and [Decentralized Identity Foundation (DIF) DIDcomm](https://identity.foundation/didcomm-messaging/spec/) messaging protocol for integration with ITN Core Services, other Agents, and Legacy Applications via REST APIs
- Authentication service utilizing [OAuth2 for REST APIs](https://oauth.net/2/) and [DIF DID AuthN](https://identity.foundation/working-groups/authentication.html) for DIF DIDcomm messaging
- An implementation of the [W3C Universal Wallet](https://w3c-ccg.github.io/universal-wallet-interop-spec/) for key and document management
- An implementation of the W3C Verifiable Credential Issuance and Verification standard both as REST APIs and as DIDcomm endpoints
- Implementation of ITN Data Sharing APIs both as REST APIs and as DIDcomm endpoints
- An implementation of ITN Core Services functionality via DIF DIDcomm
- An implementation of the [W3C/DIF Encrypted Data Vault](https://identity.foundation/confidential-storage/) for document storage

ITN Agent Integration can be achieved through an SDK, as utilized by the ITN.

![ITN Agent](./docs/images/ITN%20Agent.jpg)

ITN Agent is an implementation of the [Aries Agent RFC](https://github.com/hyperledger/aries-rfcs/blob/main/concepts/0004-agents/README.md)
with a flexible plugin architecture.

Depending on which plugins and [DIDComm protocols](https://didcomm.org/search/?page=1) you use, your instance of ITN Agent can
perform a variety of roles:

- Create and manage cryptographic keys
- Create and manage Decentralized Identifiers
- Issue and verify Verifiable Credentials (VCs) and Presentations (VPs)
- Communicate with other Agents over [DIDComm](https://identity.foundation/didcomm-messaging/spec/#discover-features-protocol-20)

Agent provides a common interface for plugins to expand its functionality. It is designed to be modular and extensible,
so you can add new protocols, transports, and managers to fit your needs.

## ITN SDK

![ITN_SDK](https://github.com/itn-trust/itn-did-spec/assets/18353464/6135b073-c085-41fd-8ea4-f80e64e0485d)

The ITN SDK has an Identity Protocol provides methods to manage DIDs.
The ITN SDK has a DID Document Manager provide a set of functions to manage DID Documents.

## Conformance

The keywords MAY, MUST, MUST NOT, RECOMMENDED, SHOULD, and SHOULD NOT in this document are to be interpreted according to [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119)`

## Terminology

1. ITN - Integrated Trust Network
2. EDV - Encrypted Data Vault
3. CAS - Content Addressable Storage
4. DLT - Distributed Ledger Network
5. DID - Decentralized Identifier
6. VC - Verifiable Credentials
7. VP - Verifiable Presentations

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

Requirements:

- `creator` input parameter can be a DID String or a DID document.
- The provided DID MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.

---

#### Resolve

Description: Based on the DID data schema context file and DID method an ITN process delivers a DID document in accordance with the W3C DID standard.

Requirements:

- A DID string MUST be provided as an input parameter.
- The provided DID MUST be compliant with the did:itn method.

---

#### Update

Description: Based on the DID data schema context file and DID method an ITN process creates an updated DID document in accordance with the W3C DID standard.

Requirements:

- `receiver` input parameter can be a DID string or a DID Document.
- The provided DID string MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.

---

#### Revoke

Description: Based on the DID data schema context file and DID method an ITN process revokes an existing DID and its DID document in accordance with the W3C DID standard.

Requirements:

- `did` input parameter MUST be a DID string that has to be revoked.
- `receiver` input parameter can be a DID string or a DID Document.
- The provided DID string MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.

---

#### Recover

Description: Based on the DID data schema context file and DID method, an ITN process recovers a DID and its DID document in accordance with the W3C DID standard.

Requirements:

- `did` input parameter MUST be a DID string that has to be recovered.
- `receiver` input parameter can be a DID string or a DID Document.
- The provided DID string MUST be compliant with the did:itn method.
- The provided DID document MUST be compliant with the DID V1.0 DID document specification.

---

#### Deactivate

The `revoke` operation also deactivate the DID.

## Sample DID Document

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

## Security Considerations

For all `did:itn` DIDs, the initial asset creation, and subsequent updates are executed using `Ed25519` keys, which are widely recognized as a robust and secure cryptographic mechanism. `Confirm With Umed & Andreas - if the keys are different. DELETE THIS COMMENT LATER.`

- Authentication service utilizing [OAuth2](https://oauth.net/2/) for REST APIs and [DIF DID AuthN](https://identity.foundation/working-groups/authentication.html) for DIF DIDcomm messaging
- Keys are specified in the DID Document in Verification Methods object for various Verification Relationships.
- ITN heavily relies on W3C DIDs (Decentralized Identifiers) and VCs (Verifiable Credentials) cryptographic mechanisms and decentralized trust models to mitigate the risks associated with various attacks.

Here's how ITN can prevent the specific attacks outlined:

1. Eavesdropping

ITN DID can facilitate the establishment of secure communication channels between parties by encrypting messages exchanged between DIDs using cryptographic keys defined in the corresponding DID Documents. DIDs and VCs use cryptographic signatures to ensure that communication between parties is encrypted and authenticated. This prevents unauthorized interception and eavesdropping of sensitive information.

2. Replay

Digital signatures utilized between the ITN and a user utilize nonces and DLT transactions on either of the utilized networks utilize both chainIds and account nonces to prevent DLT transaction replays.

3. Message Insertion

Since ITN communication channels are using asymmetric encryption, the only way to insert a message would be to gain access to the private keys used for encryption, DID AuthN, and VC signatures.

4. Deletion:

The DLT transactions on the two anchor DLTs cannot be deleted and DID document entries in the CAS can only be deleted by ITN Node Operators. ITN Node Operators are permissioned, and known legal entities. In addition, currently, only three ITN nodes have write permissions into the CAS. Lastly, existing CAS data redundancies make a CAS deletion not practical. 

5. Modification:

Modifications of the DID anchoring transactions on the two DLTs cannot be modified. CAS DID doc entries could only be maliciously modified by three specific ITN Nodes. Furthermore, data redundancies with rollbacks make modification attacks useless. 

6. Denial of Service (DoS):

The ITN can operate as long as one honest node is operational. This means that besides typical (D)DoS protections enterprises utilize, the node redundancies in the network mitigate a DoS attack.

7. Amplification:

All DID operations apart from DID creation require DID AuthN. Therefore, each attack on a DID operation endpoint requires a DID and a secure communications channel. Hence, any attacks are limited to that established channel which can be disconnected at any time by an Operator. Lastly, the DID creation operation also requires establishing a secure communications channel which again restricts any attack to that channel, and, therefore, mitigation is as already mentioned. DOSing a channel would lead to a blacklisting of the malicious DID and a new DID would have to be created to perform an attack. Since the network only requires one honestly operating node, attacks by individual nodes would be recognized by other network nodes and access to that node can be easily removed by each node operator in their node, isolating the malicious node. And since network transactions have to be reimbursed to the ITN as an organization, spamming attacks of a compromised node lead to economic damage to the Operator operating the malicious node. This is an economic security guarantee since all Node Operators are known legal entities to which exists legal recourse. 

8. Man-in-the-Middle (MitM):

Asymmetric encryption of all ITN communication channels ensures a high degree of safety against MitM attacks unless the relevant private keys are compromised.

**Note:** Security Considerations section ensures the ITN DID Method Security considerations comply with [W3C DID Method Security Requirements](https://w3c.github.io/did-core/#security-requirements)

## Privacy Considerations

ITN is only storing the following data:

- Public Keys in DID documents stored in our CAS, and in Smart Contracts stored on our DLTs
- Hashed data in Smart Contracts on our DLTs representing DID documents, in our CAS for our VC revocation lists, and aggregate application state data
- Service endpoints in DID documents on our CAS

In the ITN context, only Public Keys and Service Endpoints may refer to an individual. ITN ensures proper management in the following way:

1. DID documents may be deleted by their controllers, and, therefore, access to DID documents from the outside by a 3rd party is no longer possible.
2. While public keys and hashes of DID documents remain on the DLTs of the ITN, ITN will not maintain logs of IP addresses that requested ITN service operations, and, therefore, makes the correlation of a public key with personal data (the IP assigned to an individual’s router at a specific physical address by the ISP) from the point of view of the ITN, or an entity breaching the ITN, not possible. However, if a public key is used outside of the context of ITN more than once, then it may be subject to linkability to other personal data.

The reason why within ITN hashed data may be considered sufficiently anonymized is as follows: While hashed data may contain personal data through public keys or service endpoints in the DID documents, it is combined with randomized, non-personal data such as a DID itself, per European Commission (EC) guideline.

**Note:** Privacy Considerations section ensures the ITN DID Method Privacy considerations complies with [W3C DID Method Privacy Requirements](https://w3c.github.io/did-core/#privacy-requirements)

## Appendix

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

## Message Encryptor

Provides a set of functions to encrypt and decrypt DIDComm messages. Message Encryptor supports two forms of message
encryption: Authenticated Sender Encryption (`authcrypt`) and Anonymous Sender Encryption (`anoncrypt`).
Both forms are encrypted to the recipient DID. Only `authcrypt` provides direct assurances of who the sender is.

#### Message Encryptor supports the following curves for the keys involved in key agreement

| Curve  | Description                                                                                                                           |
|--------|---------------------------------------------------------------------------------------------------------------------------------------|
| X25519 | The underlying curve is actually Curve25519, however when used in the context of Diffie-Hellman<br/> the identifier of X25519 is used |
| P-256  | NIST defined P-256 elliptic curve.                                                                                                    |
| P-384  | NIST defined P-384 elliptic curve.                                                                                                    |
| P-521  | NIST defined P-521 elliptic curve.                                                                                                    |

#### The following `AEAD` algorithms are supported for content encryption of the message

| Algorithm     | Description                                 |
|---------------|---------------------------------------------|
| A256CBC-HS512 | AES256-CBC + HMAC-SHA512 with a 512 bit key |
| A256GCM       | AES256-GCM with a 256 bit key               |
| XC20P         | XChaCha20Poly1305 with a 256 bit key        |

#### The following Key Wrapping Algorithms are supported to wrap shared secret key

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

## References

[1] W3C Decentralized Identifiers (DIDs) v1.0, <https://w3c.github.io/did-core/>
