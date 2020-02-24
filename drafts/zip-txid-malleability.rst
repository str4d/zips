::

  ZIP: XXX
  Title: Non-Malleable Transaction IDs
  Owners: Jack Grigg <jack@electriccoin.co>
  Status: Draft
  Category: Consensus
  Created: 2019-08-23
  License: MIT


Terminology
===========

The key words "MUST" and "MAY" in this document are to be interpreted as described in
RFC 2119. [#RFC2119]_

The term "network upgrade" in this document is to be interpreted as described in ZIP 200
[#zip-0200]_.

The term "Sapling" in this document is to be interpreted as described in ZIP 205
[#zip-0205]_.

Abstract
========

This proposal defines a new transaction format
complex forms of transparent output predicates ("transparent programs") to be deployed
in network upgrades, while ensuring that a transaction's ID is non-malleable if the
transaction uses any transparent programs.


Motivation
==========

Some off-chain protocols require that transaction IDs be non-malleable in order to be
secure. Zcash transaction IDs are currently malleable in several ways:

- The ``scriptSig`` field of transparent inputs is not part of the data that is hashed for
  signing (as the signature is placed in that field), but is part of the data that is
  hashed to produce the transaction ID. An adversary can modify this field in a variety of
  ways that do not functionally alter it, but change its binary representation. This is
  the core transaction malleability problem that Bitcoin mitigated with their Segregated
  Witness soft fork [#bip-0141]_ (for transactions that opt-in to SegWit-only inputs).

- The Ed25519 signature ``joinSplitSig`` is malleable. The known sources of malleability
  are:

  - The y-coordinate of the R component of the signature is not required to be canonical.
  - There are equivalent points for R because its order may be greater than the subgroup
    order ``ell``.

  These malleability sources correspond to the verification process in ``libsodium``. Note
  that the public key ``joinSplitPubKey`` is also not required to be canonical, but this
  is not a source of malleability because the public key is hashed as given in the
  transaction when verifying the signature.

- RedJubjub signatures, as currently enforced by the consensus rules, are malleable. This
  is because the signature verification algorithm [#redjubjub]_ multiplies through by the
  cofactor before the identity check (which ensures that the single and batch verification
  algorithms agree on the set of valid signatures). An adversary can modify the signature
  by adding a nonzero point of small order to R, leaving the signature valid but altering
  its binary representation.

  - TODO: Upon further investigation, it looks like RedJubjub is not actually malleable
    for the above reason. We are analysing this and will update the proposal when we are
    certain. It is also possible that Ed25519 might also be non-malleable for a similar
    reason, but verifying this would require very careful inspection of libsodium; it is
    simpler to just exclude them.


Conventions
===========

We use the following notation as defined in the Zcash protocol specification
[#spec-notation]_, reproduced here for convenience:

- length(*S*) means the length of (number of elements in) *S*.

- { *a* .. *b* } means the set or type of integers from *a* through *b* inclusive.

- *a* || *b* means the concatenation of sequences *a* then *b*.


Specification
=============

Asset definitions
-----------------



Modifications to the circuit
----------------------------



Encoding in transactions
------------------------



Consensus rules
---------------

Once the TODO network upgrade activates, the following new consensus rules are enforced:

- The ``PROGRAM`` opcode MUST NOT be present anywhere in ``scriptPubKey`` or ``scriptSig``
  except at the beginning.

Rationale
=========




Security and Privacy Considerations
===================================




Reference Implementation
========================

TBD


Acknowledgements
================




References
==========

.. [#RFC2119] `Key words for use in RFCs to Indicate Requirement Levels <https://tools.ietf.org/html/rfc2119>`_
.. [#zip-0200] `ZIP 200: Network Upgrade Activation Mechanism <https://github.com/zcash/zips/blob/master/zip-0200.rst>`_
.. [#zip-0205] `ZIP 205: Deployment of the Sapling Network Upgrade <https://github.com/zcash/zips/blob/master/zip-0205.rst>`_
.. [#bip-0141] `BIP 141: Segregated Witness (Consensus layer) <https://github.com/bitcoin/bips/blob/master/bip-0141.mediawiki>`_
.. [#redjubjub] `Section 5.4.6: RedDSA and RedJubjub. Zcash Protocol Specification, Version 2019.0.2 [Overwinter+Sapling] <https://github.com/zcash/zips/blob/master/protocol/protocol.pdf>`_
.. [#spec-notation] `Section 2: Notation. Zcash Protocol Specification, Version 2019.0.2 [Overwinter+Sapling] <https://github.com/zcash/zips/blob/master/protocol/protocol.pdf>`_
.. [#sapling-gvc] `Comment on Generalized Value Commitments <https://github.com/zcash/zcash/issues/2277#issuecomment-321106819>`_
.. [#batch-verification] `Section B.1: RedDSA batch verification. Zcash Protocol Specification, Version 2019.0.2 [Overwinter+Sapling] <https://github.com/zcash/zips/blob/master/protocol/protocol.pdf>`_
