# arrtOS

A Guide Explaining the process of development of arrtOS.

## Step - I

1. Create a version-check.sh file

```bash
touch version-check.sh
```

2. Paste these contents to version-check.sh file

```bash
#!/bin/bash
# Simple script to list version numbers of critical development tools
export LC_ALL=C
bash --version | head -n1 | cut -d" " -f2-4
MYSH=$(readlink -f /bin/sh)
echo "/bin/sh -> $MYSH"
echo $MYSH | grep -q bash || echo "ERROR: /bin/sh does not point to bash"
unset MYSH

echo -n "Binutils: "; ld --version | head -n1 | cut -d" " -f3-
bison --version | head -n1

if [ -h /usr/bin/yacc ]; then
  echo "/usr/bin/yacc -> `readlink -f /usr/bin/yacc`";
elif [ -x /usr/bin/yacc ]; then
  echo yacc is `/usr/bin/yacc --version | head -n1`
else
  echo "yacc not found"
fi

echo -n "Coreutils: "; chown --version | head -n1 | cut -d")" -f2
diff --version | head -n1
find --version | head -n1
gawk --version | head -n1

if [ -h /usr/bin/awk ]; then
  echo "/usr/bin/awk -> `readlink -f /usr/bin/awk`";
elif [ -x /usr/bin/awk ]; then
  echo awk is `/usr/bin/awk --version | head -n1`
else
  echo "awk not found"
fi

gcc --version | head -n1
g++ --version | head -n1
grep --version | head -n1
gzip --version | head -n1
cat /proc/version
m4 --version | head -n1
make --version | head -n1
patch --version | head -n1
echo Perl `perl -V:version`
python3 --version
sed --version | head -n1
tar --version | head -n1
makeinfo --version | head -n1  # texinfo version
xz --version | head -n1

echo 'int main(){}' > dummy.c && g++ -o dummy dummy.c
if [ -x dummy ]
  then echo "g++ compilation OK";
  else echo "g++ compilation failed"; fi
rm -f dummy.c dummy
```

3. Run the version-check.sh file

```bash
bash version-check.sh
```

**Note :** If /bin/sh does not point to bash throws an error, then run this

```bash
sudo ln -sf bash /bin/sh
```

4. If any of the required package shows an error as command not found then install the respective packages.

```bash
sudo apt install <package-name>
```

## Now, preparing for the build

5. export the DISTRO_ROOT variable

```bash
export DISTRO_ROOT=./arrtOS_DISTRO_ROOT
```

6. export the LFS variable

```bash
export LFS=$DISTRO_ROOT/build_env/build_root
```

7. Create all necessary directories

```bash
mkdir -p $LFS
```

8. Create a file package_list inside the arrtOS_DISTRO_ROOT/build_env and paste the following urls

```bash
https://download.savannah.gnu.org/releases/acl/acl-2.3.1.tar.xz
https://download.savannah.gnu.org/releases/attr/attr-2.5.1.tar.gz
https://ftp.gnu.org/gnu/autoconf/autoconf-2.71.tar.xz
https://ftp.gnu.org/gnu/automake/automake-1.16.5.tar.xz
https://ftp.gnu.org/gnu/bash/bash-5.1.16.tar.gz
https://github.com/gavinhoward/bc/releases/download/5.2.2/bc-5.2.2.tar.xz
https://ftp.gnu.org/gnu/binutils/binutils-2.38.tar.xz
https://ftp.gnu.org/gnu/bison/bison-3.8.2.tar.xz
https://www.sourceware.org/pub/bzip2/bzip2-1.0.8.tar.gz
https://github.com/libcheck/check/releases/download/0.15.2/check-0.15.2.tar.gz
https://ftp.gnu.org/gnu/coreutils/coreutils-9.0.tar.xz
https://dbus.freedesktop.org/releases/dbus/dbus-1.12.20.tar.gz
https://ftp.gnu.org/gnu/dejagnu/dejagnu-1.6.3.tar.gz
https://ftp.gnu.org/gnu/diffutils/diffutils-3.8.tar.xz
https://downloads.sourceforge.net/project/e2fsprogs/e2fsprogs/v1.46.5/e2fsprogs-1.46.5.tar.gz
https://sourceware.org/ftp/elfutils/0.186/elfutils-0.186.tar.bz2
https://prdownloads.sourceforge.net/expat/expat-2.4.6.tar.xz
https://prdownloads.sourceforge.net/expect/expect5.45.4.tar.gz
https://astron.com/pub/file/file-5.41.tar.gz
https://ftp.gnu.org/gnu/findutils/findutils-4.9.0.tar.xz
https://github.com/westes/flex/releases/download/v2.6.4/flex-2.6.4.tar.gz
https://ftp.gnu.org/gnu/gawk/gawk-5.1.1.tar.xz
https://ftp.gnu.org/gnu/gcc/gcc-11.2.0/gcc-11.2.0.tar.xz
https://ftp.gnu.org/gnu/gdbm/gdbm-1.23.tar.gz
https://ftp.gnu.org/gnu/gettext/gettext-0.21.tar.xz
https://ftp.gnu.org/gnu/glibc/glibc-2.35.tar.xz
https://ftp.gnu.org/gnu/gmp/gmp-6.2.1.tar.xz
https://ftp.gnu.org/gnu/gperf/gperf-3.1.tar.gz
https://ftp.gnu.org/gnu/grep/grep-3.7.tar.xz
https://ftp.gnu.org/gnu/groff/groff-1.22.4.tar.gz
https://ftp.gnu.org/gnu/grub/grub-2.06.tar.xz
https://ftp.gnu.org/gnu/gzip/gzip-1.11.tar.xz
https://github.com/Mic92/iana-etc/releases/download/20220207/iana-etc-20220207.tar.gz
https://ftp.gnu.org/gnu/inetutils/inetutils-2.2.tar.xz
https://launchpad.net/intltool/trunk/0.51.0/+download/intltool-0.51.0.tar.gz
https://www.kernel.org/pub/linux/utils/net/iproute2/iproute2-5.16.0.tar.xz
https://files.pythonhosted.org/packages/source/J/Jinja2/Jinja2-3.0.3.tar.gz
https://www.kernel.org/pub/linux/utils/kbd/kbd-2.4.0.tar.xz
https://www.kernel.org/pub/linux/utils/kernel/kmod/kmod-29.tar.xz
https://www.greenwoodsoftware.com/less/less-590.tar.gz
https://www.kernel.org/pub/linux/libs/security/linux-privs/libcap2/libcap-2.63.tar.xz
https://github.com/libffi/libffi/releases/download/v3.4.2/libffi-3.4.2.tar.gz
https://download.savannah.gnu.org/releases/libpipeline/libpipeline-1.5.5.tar.gz
https://ftp.gnu.org/gnu/libtool/libtool-2.4.6.tar.xz
https://www.kernel.org/pub/linux/kernel/v5.x/linux-5.16.9.tar.xz
https://ftp.gnu.org/gnu/m4/m4-1.4.19.tar.xz
https://ftp.gnu.org/gnu/make/make-4.3.tar.gz
https://download.savannah.gnu.org/releases/man-db/man-db-2.10.1.tar.xz
https://www.kernel.org/pub/linux/docs/man-pages/man-pages-5.13.tar.xz
https://files.pythonhosted.org/packages/source/M/MarkupSafe/MarkupSafe-2.0.1.tar.gz
https://github.com/mesonbuild/meson/releases/download/0.61.1/meson-0.61.1.tar.gz
https://ftp.gnu.org/gnu/mpc/mpc-1.2.1.tar.gz
https://www.mpfr.org/mpfr-4.1.0/mpfr-4.1.0.tar.xz
https://invisible-mirror.net/archives/ncurses/ncurses-6.3.tar.gz
https://github.com/ninja-build/ninja/archive/v1.10.2/ninja-1.10.2.tar.gz
https://www.openssl.org/source/openssl-3.0.1.tar.gz
https://ftp.gnu.org/gnu/patch/patch-2.7.6.tar.xz
https://www.cpan.org/src/5.0/perl-5.34.0.tar.xz
https://pkg-config.freedesktop.org/releases/pkg-config-0.29.2.tar.gz
https://sourceforge.net/projects/procps-ng/files/Production/procps-ng-3.3.17.tar.xz
https://sourceforge.net/projects/psmisc/files/psmisc/psmisc-23.4.tar.xz
https://www.python.org/ftp/python/3.10.2/Python-3.10.2.tar.xz
https://www.python.org/ftp/python/doc/3.10.2/python-3.10.2-docs-html.tar.bz2
https://ftp.gnu.org/gnu/readline/readline-8.1.2.tar.gz
https://ftp.gnu.org/gnu/sed/sed-4.8.tar.xz
https://github.com/shadow-maint/shadow/releases/download/v4.11.1/shadow-4.11.1.tar.xz
https://github.com/systemd/systemd/archive/v250/systemd-250.tar.gz
https://anduin.linuxfromscratch.org/LFS/systemd-man-pages-250.tar.xz
https://ftp.gnu.org/gnu/tar/tar-1.34.tar.xz
https://downloads.sourceforge.net/tcl/tcl8.6.12-src.tar.gz
https://downloads.sourceforge.net/tcl/tcl8.6.12-html.tar.gz
https://ftp.gnu.org/gnu/texinfo/texinfo-6.8.tar.xz
https://www.iana.org/time-zones/repository/releases/tzdata2021e.tar.gz
https://www.kernel.org/pub/linux/utils/util-linux/v2.37/util-linux-2.37.4.tar.xz
https://anduin.linuxfromscratch.org/LFS/vim-8.2.4383.tar.gz
https://cpan.metacpan.org/authors/id/T/TO/TODDR/XML-Parser-2.46.tar.gz
https://tukaani.org/xz/xz-5.2.5.tar.xz
https://zlib.net/zlib-1.2.11.tar.xz
https://github.com/facebook/zstd/releases/download/v1.5.2/zstd-1.5.2.tar.gz
https://www.linuxfromscratch.org/patches/lfs/11.1/binutils-2.38-lto_fix-1.patch
https://www.linuxfromscratch.org/patches/lfs/11.1/bzip2-1.0.8-install_docs-1.patch
https://www.linuxfromscratch.org/patches/lfs/11.1/coreutils-9.0-i18n-1.patch
https://www.linuxfromscratch.org/patches/lfs/11.1/coreutils-9.0-chmod_fix-1.patch
https://www.linuxfromscratch.org/patches/lfs/11.1/glibc-2.35-fhs-1.patch
https://www.linuxfromscratch.org/patches/lfs/11.1/kbd-2.4.0-backspace-1.patch
https://www.linuxfromscratch.org/patches/lfs/11.1/perl-5.34.0-upstream_fixes-1.patch
https://www.linuxfromscratch.org/patches/lfs/11.1/systemd-250-upstream_fixes-1.patch
```

9. Create a prepare.sh file

```bash
DISTRO_ROOT=/home/<your-username>/arrtOS/arrtOS_DISTRO_ROOT/
LFS=$BUILD_ROOT/build_env/build_root

mkdir -p $LFS/sources

for f in $(cat $DISTRO_ROOT/build_env/package_list)
do
  bn=$(basename $f)

  if ! test -f $LFS/sources/$bn ; then
    wget $f -O $LFS/sources/$bn
  fi
done;
```

10. Run the prepare.sh file

```bash
sudo sh ./prepare.sh
```
