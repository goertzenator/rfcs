---
feature: (fill me in with a unique ident, my_awesome_feature)
start-date: (fill me in with today's date, YYYY-MM-DD)
author: (name of the main author)
co-authors: (find a buddy later to help our with the RFC)
related-issues: (will contain links to implementation PRs)
---

# Summary
[summary]: #summary

Expand x86 32 bit support beyond i686 to include i586, i486, and i386.  This biggest part of this change is to disambiguate usages of `i686` which is currently overloaded to mean both "32 bit x86" and "exactly the i686 architecture".  The term `x86_32` is proposed here to replace many current usages of `i686`.


# Motivation
[motivation]: #motivation

- To support embedded x86 SoCs such as the i586 [Vortex86](https://en.wikipedia.org/wiki/Vortex86).  I personally would like to use nix on such a CPU.
- To provide the framework for other contributers to expand support for these architectures.

# Detailed design
[design]: #detailed-design

The primary part that requires discussion is disambiguation of `i686`.  After that is resolved the addition of `i[345]86` support is less controversial.

Introduce the predicate `isx86_32` which is symmetric to the already existing `isx86_64`.  `isx86_32` will mean "32 bit x86" and the existing `isi686` will be reinterpreted as "exactly i686 architecture".

For i686 this change is effectively a low-risk no-op because `isx86_32` and `isi686` will produce the same result.  Only when the CPU is changed to `i[345]86` does the distinction begin to matter.

Once the above change is agreed upon then the addition of `i[345]86` CPUs becomes straight forward.  Predicates `isi586`, `isi485`, and `isi386` will be added.  Usages of `isi686` in various packages will be flipped to `isx86_32` as needed to make them work on these CPUs.

Pragmatically, I will be looking to enable i586 support on several common packages including linux kernel, libc, and openssh.  I'm not interested in i486 and i386, but it is trivial to add these CPU definitions alongside i586 should someone in future want to take a crack at it.


# Drawbacks
[drawbacks]: #drawbacks

- i586 and predecessors are already uncommon and use continues to diminish.

# Alternatives
[alternatives]: #alternatives

- Use `x86` instead of `x86_32`: This term already has amiguity and should be avoided.  nix considers x86_64 to be in the x86 "family".  The linux kernel considers 32 and 64 bit intel CPUs to all be "x86".  `x86_32` removes all opportunity for confusion.
- `isexactlyi686`: In addition to adding `isx86_32`, rename `isi686` as `isexactlyi686`.  This intentionally breaks all current uses of `isi686` and forces developers to decide whether `isx86_32` or `isexactlyi686` was intended.  This quickly gets us to a more "correct" nixpkgs but the risk of breakage for innocent bystanders is likely not worth it.


# Unresolved questions
[unresolved]: #unresolved-questions

Other variants such as Cyrix 6x86, which is somewhere between i586 and i686, are not considered here.

Should the x86 CPUs have a version number like the ARM CPUs do?

# Future work
[future]: #future-work

None known.
