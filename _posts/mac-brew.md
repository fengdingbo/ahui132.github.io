# brew
- brew(Homebrew) 是近来极流行的 安装gawk,gsed,macvim等命令的安装工具，所有的包都被安装到/usr/local/Cellar下，然后再以`ln -s` 软链接的形式链接到`/usr/local/`目录下

- Homebrew-cask是一套建立在homebrew基础上的Mac软件安装命令行工具 ，有了它再也不想用dmg了（每次都要下载，不停的点下一步，拖放多麻烦呀）

## install brew

	ruby -e "$(curl -fsSL https://raw.github.com/mxcl/homebrew/go)"

	# install gsed
	brew install gsed
	# intall macvim
	brew install macvim --override-system-vim
	# brew link php54
	brew unlink php54 && brew link php54

所有的安装文件都是放在.(通过link连接到其它的地方, 非常方便管理)

	ls /usr/local/Cellar

查看单个包的安装目录：

	$ brew --prefix homebrew/php/php55
	/usr/local/Cellar/php55/5.5.16

### brew update
Update formulae

### brew prune

	brew prune [--dry-run]:
	    Remove dead symlinks

### brew edit
Edit formulae

	brew edit <softname>
	brew edit emacs

### install cli program via brew

	brew install vim                 # installs from Homebrew/homebrew
	brew install username/repo/vim   # installs from your custom repo

## brew tap
brew tap allows you to add more Github repos to the list of formulae that brew tracks, updates and installs from.

	brew tap; #list all tap
	brew tap username/repo
	brew untap phinze/cask

	brew tap caskroom/cask
	brew install brew-cask
	brew cask install virtualbox
	brew cask install virtualbox-extension-pack

## brew upgrade
find all outdated packages:

	brew outdated

upgrade all packages except mysql

	brew pin mysql
	brew upgrade

## install brew-cask
brew-cask 相当于app store的命令版, app管理方式更优雅 更简洁
所有安装的软件位置: `/opt/homebrew-cask/Caskroom`

	brew install caskroom/cask/brew-cask
	brew upgrade brew-cask
	brew upgrade; # upgrade all packages

使用：

	# install chrome & alfred2 ...
	brew cask install chrome Alfred2 qq
	# cleanup cached downloads
	brew cask cleanup
	# uninstall qq
	brew cask uninstall qq
	# update app
	brew cask uninstall APP && brew cask install APP
	# Alfred : just add manage the scope addition
	brew cask alfred link

Refer to : [brew-cask](http://ksmx.me/blog/2013/10/05/homebrew-cask-cli-workflow-to-install-mac-applications/)

> 在Alfred中添加brew cask安装程序的路径，`/opt/homebrew-cask/Caskroom`

### debug

	 brew update && brew upgrade brew-cask
	 brew cleanup && brew cask cleanup

# write formula
https://github.com/Anakros/homebrew-tsocks

class Pdf2htmlex < Formula
  desc "PDF to HTML converter"
  homepage "https://coolwanglu.github.io/pdf2htmlEX/"
  url "https://github.com/coolwanglu/pdf2htmlEX/archive/v0.14.6.tar.gz"
  sha256 "320ac2e1c2ea4a2972970f52809d90073ee00a6c42ef6d9833fb48436222f0e5"
  revision 2

  head "https://github.com/coolwanglu/pdf2htmlEX.git"

  bottle do
    sha256 "32e5f50b54614103ef8fcbea86f078b3502a1483972f92eaf1ab2be8e4992b54" => :el_capitan
    sha256 "1f7514ea957b7d9bc932637858b2538e6d6919e607b1af8b25ae00daf666b53c" => :yosemite
    sha256 "d2ff6e221fb723ce918fcfd4d1844ec78a6a1d87d0471db9246c06a41816c9d7" => :mavericks
  end

  depends_on :macos => :lion
  depends_on "cmake" => :build
  depends_on "pkg-config" => :build
  depends_on "poppler"
  depends_on "gnu-getopt"
  depends_on "ttfautohint" => :recommended if MacOS.version > :snow_leopard

  # Fontforge dependencies
  depends_on "autoconf" => :build
  depends_on "automake" => :build
  depends_on "libtool" => :run
  depends_on "glib"
  depends_on "pango"
  depends_on "gettext"
  depends_on "libpng"   => :recommended
  depends_on "jpeg"     => :recommended
  depends_on "libtiff"  => :recommended

  # Pdf2htmlex use an outdated, customised Fontforge installation.
  # See https://github.com/coolwanglu/pdf2htmlEX/wiki/Building
  resource "fontforge" do
    url "https://github.com/coolwanglu/fontforge.git", :branch => "pdf2htmlEX"
  end

  # And failures
  fails_with :llvm do
    build 2336
    cause "Compiling cvexportdlg.c fails with error: initializer element is not constant"
  end

  def install
    resource("fontforge").stage do
      args = %W[
        --prefix=#{prefix}/fontforge
        --without-libzmq
        --without-x
        --without-iconv
        --disable-python-scripting
        --disable-python-extension
      ]

      # Fix linker error; see: https://trac.macports.org/ticket/25012
      ENV.append "LDFLAGS", "-lintl"

      # Reset ARCHFLAGS to match how we build
      ENV["ARCHFLAGS"] = "-arch #{MacOS.preferred_arch}"

      system "./autogen.sh"
      system "./configure", *args

      system "make"
      system "make", "install"
    end

    # Prepend the paths to always find this dep fontforge instead of another.
    ENV.prepend_path "PKG_CONFIG_PATH", "#{prefix}/fontforge/lib/pkgconfig"
    ENV.prepend_path "PATH", "#{prefix}/fontforge/bin"
    system "cmake", ".", *std_cmake_args
    system "make"
    system "make", "install"
  end

  test do
    system "#{bin}/pdf2htmlEX", test_fixtures("test.pdf")
  end
end
