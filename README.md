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

The ITN DID is specified in accordance with the [W3C DID Core specification](https://www.w3.org/TR/did-core/). 

The syntax of the identifier and accompanying data model used by the protocol is conformant with the [DID-CORE](https://www.w3.org/TR/did-core/) specification and shall be registered with the [DID-SPEC-REGISTRIES](https://www.w3.org/TR/did-spec-registries/).


## Conformance

The keywords MAY, MUST, MUST NOT, RECOMMENDED, SHOULD, and SHOULD NOT in this document are to be interpreted... TBD `Not sure if required`

## Terminology

1. ITN - Integrated Trust Network `Not sure if required`

## DID ITN Method Specification

### Format

The ITN DID is derived as the sha3-256 hash of a random salt of sufficient entropy.

The format of ITN DID conforms to the [W3C DID Core specification](https://www.w3.org/TR/did-core/). It consists of `did:itn` prefix followed by `sha3-256(random salt)`. The W3C DID Core specification does not specify how a DID is generated, and leaves it up to the implementation provided it ensures uniqueness to a high degree of confidence.

**Note:** Format section ensures the ITN DID Format complies with [W3C DID Method Syntax](https://w3c.github.io/did-core/#method-syntax)

### Operations

Text - TBD

#### Create

Text - TBD

#### Read

Text - TBD

#### Update

Text - TBD

#### Deactivate

Text - TBD

**Note:** Operations section ensures the ITN DID Method Operations complies with [W3C DID Method Operations](https://w3c.github.io/did-core/#method-operations)

## Security Considerations

Text - TBD

**Note:** Security Considerations section ensures the ITN DID Method Security considerations complies with [W3C DID Method Security Requirements](https://w3c.github.io/did-core/#security-requirements)

## Privacy Considerations

Text - TBD

**Note:** Privacy Considerations section ensures the ITN DID Method Privacy considerations complies with [W3C DID Method Privacy Requirements](https://w3c.github.io/did-core/#privacy-requirements)

## Apendix

Text - TBD `Not sure if required`

## References

Text - TBD `Not sure if required`