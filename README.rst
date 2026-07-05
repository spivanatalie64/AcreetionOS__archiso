======
AcreetionOS__archiso
======

AcreetionOS' fork of `archiso <https://gitlab.archlinux.org/archlinux/archiso>`_ —
the official tooling for building Arch Linux ISO images.

This fork extends upstream archiso with:

* **DE (Desktop Environment) overlay support** — ``DE/`` directories in edition
  profiles are automatically merged into ``airootfs/`` at build time, allowing
  desktop-specific configurations to live separately from shared ISO scaffolding.
* Optimized defaults for AcreetionOS community editions.
* Colorful ``mkarchiso`` C wrapper for better build output.

Directory Structure
===================

::

   AcreetionOS__archiso/
   ├── mkarchiso              ← Main ISO build script (modified upstream)
   ├── configs/
   │   ├── baseline/          ← Minimal profile template
   │   └── releng/            ← Release engineering profile template
   ├── docs/
   │   ├── README.profile.rst ← Profile structure documentation (includes DE docs)
   │   └── README.transfer.rst
   ├── man/
   │   ├── mkarchiso.1.rst
   │   └── variables.rst
   ├── LICENSE
   ├── Makefile
   └── README.rst             ← This file

DE Overlay Feature
==================

The ``DE/`` directory is an AcreetionOS extension to the standard archiso profile
structure. It allows edition repositories to keep desktop environment configurations
separate from the shared ISO scaffolding.

When ``mkarchiso`` builds an ISO, it:

1. Copies ``airootfs/`` to the build directory (standard upstream behavior)
2. **Merges ``DE/`` into ``airootfs/``** (AcreetionOS extension)
3. Copies the merged result to the pacstrap directory
4. Installs packages
5. Runs customization scripts
6. Creates the filesystem image

This separation means that:

* **Shared ISO structure** (bootloaders, scripts, system configs) stays in ``airootfs/``
* **Desktop-specific configs** (panel layouts, window manager settings, bar configs)
  live in ``DE/``
* Multiple editions can share the same ``airootfs/`` base while having different
  ``DE/`` configurations

Usage
=====

::

   # Build an edition profile
   ./mkarchiso -v -w /tmp/work -o /tmp/out path/to/profile/

   # With the colored wrapper (if compiled)
   ./mkarchiso_wrapper -v -w /tmp/work -o /tmp/out path/to/profile/

Profile Structure
=================

::

   profile/
   ├── DE/                    ← Desktop Environment configs (ACREETIONOS EXTENSION)
   │   ├── etc/skel/.config/
   │   │   ├── sway/          (config, bar, etc.)
   │   │   ├── i3/            (config, status, etc.)
   │   │   ├── xfce4/         (panel, terminal, etc.)
   │   │   ├── openbox/       (rc.xml, menu.xml, etc.)
   │   │   └── ...
   │   ├── usr/share/
   │   │   └── backgrounds/   (Edition wallpapers)
   │   └── root/.config/      (Root user configs)
   ├── airootfs/              ← Shared ISO filesystem overlay
   ├── efiboot/               ← UEFI boot configuration
   ├── syslinux/              ← BIOS boot configuration
   ├── grub/                  ← GRUB boot configuration
   ├── packages.x86_64        ← Package list (one per line)
   ├── bootstrap_packages.x86_64
   ├── pacman.conf
   └── profiledef.sh          ← Profile metadata

See ``docs/README.profile.rst`` for full documentation.

Differences from Upstream
=========================

1. **DE/ directory support** in ``_make_custom_airootfs()`` — automatically merges
   ``DE/`` into ``airootfs/`` before building.
2. Profile documentation updated to document the ``DE/`` feature.
3. Optimized for AcreetionOS community edition build pipelines.

License
=======

GPL-3.0-or-later — same as upstream archiso.
