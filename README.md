meta-mixins-lts - kirkstone/rust
================================

A "mixin" layer for adding current Rust toolchain into the Yocto Project LTS.
At the time Kirkstone was released in May 2022 it included Rust 1.59.0, and
officially Kirkstone supports only that. This thin special-purpose mixin
layer is meant to provide a working Rust 1.75.x toolchain for Kirkstone by
backporting the appropriate recipes from the scarthgap LTS branch of
openembedded-core.

Notes
-----

- **Not all Rust version updates carried here have been individually tested!**
  Care has been taken to keep backported changes in the same order as they
  have been applied to oe-core, so the various older Rust versions should
  be buildable, but that is not guaranteed.  It is highly recommended that
  you perform your own tests for correctness when using this layer.
- cargo-update-recipe-crates.bbclass has been backported with a tweak to
  use host rather than native Python since kirkstone does not have a new
  enough Python (3.11 is required).  Using the update task in the class
  will fail unless the host environment contains Python 3.11 or newer.
- The newer version of librsvg from scarthgap has been backported since
  there is not a straightforward way to update the existing one via
  bbappend.  Backporting a working recipe should be easier to maintain
  than developing an update of the older recipe in kirkstone.
- A newer cairo recipe (1.18.0 as of August 2024) has been backported
  to enable building librsvg 2.57.1 and newer.  It is believed this has
  minimal impact on other cairo users.
- python3-cryptography has been left alone since the recipe in kirkstone
  still works with the newer toolchain, and updating it seems to have
  more potential impact due to upstream API changes and its known to be
  finicky build.  This may change if sufficient rationale for doing the
  backport becomes apparent.
- While changes to Rust recipe and class files related to oe-selftest
  support are included by necessity, no effort has been made to backport
  the actual addition of Rust to oe-selftest, as that is considered too
  invasive and maintenance heavy.
- To include Rust support in a SDK, do the following in e.g. local.conf:
  ```
  TOOLCHAIN_HOST_TASK:append = " packagegroup-rust-cross-canadian-${MACHINE}"  
  TOOLCHAIN_TARGET_TASK:append = " ${@multilib_pkg_extend(d, 'libstd-rs')}"
  ```
  Overriding populate_sdk_base.bbclass to avoid needing to do this seems
  too invasive for this layer.
- The intent is to track any further upgrades that occur in the scarthgap
  branch of oe-core, with an expected end of support in April 2026, the
  same as Yocto Project's planned EOL for Kirkstone (per
  https://wiki.yoctoproject.org/wiki/Releases).

Dependencies
------------

This layer depends on:

- URI: git://github.com/openembedded/openembedded-core.git  
  layers: meta  
  branch: kirkstone

Contributing
------------

The yocto-patches mailinglist (yocto-patches@lists.yoctoproject.org) is used
for questions, comments and patch review. It is subscriber only, so please
register before posting.

Send pull requests to yocto-patches@lists.yoctoproject.org with
'[meta-lts-mixins][kirkstone/rust]' in the subject.

When sending single patches, please use something like:
git send-email -M -1 --to=yocto-patches@lists.yoctoproject.org --subject-prefix='meta-lts-mixins][kirkstone/rust][PATCH'

Note that changes that are not direct backports from oe-core master branch, or
are not fixes for a demonstrated build breakage against Kirkstone are unlikely
to be accepted.  Functional changes should be submitted and merged to oe-core
master branch first.

Maintenance
-----------

Layer maintainers:
Scott Murray <scott.murray@konsulko.com>
