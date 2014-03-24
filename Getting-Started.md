# Introduction
Sigma is meant to be a next-gen engine design. When something is next-gen or cutting edge it means that there usually isn't something similar out yet, and as such means that the gears are well oiled and might not spin as well as they could.

The build process might seem complex, but, if followed properly, it should be painless.

## Download the Prerequisites
**Linux** download with the appropriate package manager.

**Windows** Create a new folder named something like Sigma Libraries and place the download inside (either the source or binary package).
* [GLEW](http://glew.sourceforge.net/) 1.10 - GL extensions pointer wrapper (not needed on OS X)
* [GLFW](http://www.glfw.org) 3.0.0 - platform abstraction
* [GLM](http://glm.g-truc.net) 0.9.5 - GL math
* [Bullet](http://www.bulletphysics.org) 2.81 - physics
* [OpenAL Soft](http://kcat.strangesoft.net/openal.html) 1.15.1 - OpenAL audio
* [libogg](https://www.xiph.org/ogg/) 1.3.1 - Codec for OpenAL
* [libvorbis](https://www.xiph.org/ogg/) 1.3.4 - Codec for OpenAL

## Building the Prerequisites
**Linux** This step will most likely be done when downloading from the package manager.

**Windows** You must either get the prebuilt binary package or build each library (build the **shared** version unless otherwise stated). After you build the source you will need to copy them to `sigma/lib/x86` (create this if it doesn't exist).
### GLFW
**Linux** You must use at the minimum GLFW3, so in most cases you would need to download and build this manually!

1. First, follow the instructions on the page: [GLFW Building](http://www.glfw.org/docs/latest/compile.html). (This will build as a static library by default. Leave it that way.)
2. Next, copy the libraries from `glfw/build/src/[Debug|Release]` into `sigma/lib/x86/[debug|release]`
3. Finally, copy the include folder from `glfw/include` to `sigma/include/libraries`

### GLEW
1. First, follow the instructions on the page: [GLEW Building](http://glew.sourceforge.net/build.html).
2. Next, copy the libraries from `glew/lib/[Debug|Release]/Win32` into `sigma/lib/x86/[debug|release]`
3. Finally, copy the include folder from `glew/include` to `sigma/include/libraries`

### Bullet
1. First, follow the instructions on the page: [Bullet Building](http://bulletphysics.org/mediawiki-1.5.8/index.php/Installation).
2. Next, copy the libraries from `bullet/build/lib/[Debug|Release]/Win32` into `sigma/lib/x86/[debug|release]`
3. Finally, copy the include files from `bullet/src` to `sigma/include/libraries/bullet`

### GLM.
GLM is a header only library. You must copy the `glm/glm` folder into `sigma/include/libraries/glm` (The contents of the glm/glm folder will now be in libraries/glm, or you can just copy the glm folder in glm and paste it in include/libraries.)

### OpenAL Soft
1. First, follow the instructions on the page (near the bottom): [OpenAL Soft Homepage](http://kcat.strangesoft.net/openal.html).
2. Next, copy OpenAL32.lib from `openal-soft/build/[Debug|Release]` into `sigma/lib/x86/[debug|release]`
3. Finally, copy the AL folder from `openal-soft/include` to `sigma/include/libraries`

### OggVorbis
1. First, open the libogg folder.
2. Use the appropriate build folder and files for your platform and build the static library version.
3. Copy the folder in `libbogg/inlcude` into **BOTH** the `libvorbis/include` folder and `sigma/include/libraries`
4. Go inside the libvorbis folder.
5. Use the appropriate build folder and files for your platform and build the static library version (this may error for some projects, but as long as the libvorbis_static project builds you're fine).
6. Copy the include folder from `libvorbis/include/` to `sigma/include/libraries`
7. Finally copy the lib (libogg_static.lib from **BOTH** the libvorbis and libogg _platform_/[Debug|Release] into `sigma/lib/x86/[debug|release]`