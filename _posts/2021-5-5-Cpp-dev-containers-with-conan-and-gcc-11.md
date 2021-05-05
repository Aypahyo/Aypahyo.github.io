---
layout: post
title: C++ dev container with conan and gcc11.
---

In my previous entry I explored the off the shelve container for c++ and noticed that there would be some things missing.
I am still not where I would want to be though I made some progress.

I wanted to be able to compile and run specific examples.

1. [https://docs.conan.io/en/latest/getting_started.html](conan getting started) so I know importing packages works
2. [https://en.cppreference.com/w/cpp/language/operator_comparison#Three-way_comparison](C++20 Three-way_comparison) which [https://en.cppreference.com/w/cpp/compiler_support/20](requires a gcc-10) so I know I have a modern compiler (I used gcc-11)
3. [https://www.boost.org/doc/libs/1_48_0/libs/locale/doc/html/hello_8cpp-example.html](boost local hello world) to import boot which is an end in itself

# Conan

Setting up conan was reasonably simple and straight forward.
I had to install python3-pip and use that to install conan., activate V2 mode and initialize. That was enough in order to build using console commands only.

Later when I wanted to work with gcc-11 I had to set up my local alternatives so that conan would automatically initialize with gcc-11. In order to build I had to add gcc 11 to the conan settings in order to allow this compiler.

vscode integration presented two more challenges. The smaller one requires changing the User to vscode before initializing in the dockerfile that builds the container so that all the initialization happens for the correct user. The bigger one was letting intellisense know where to look for libraries. The simple solution is to add ```/home/vscode/.conan/data/**``` to the ```includePath``` in ```c_cpp_properties.json```. That approach though will also inspect every library that is not added to the project and may also include two versions of the same library. So instead I opted for using 
```conan info --paths ..``` after the installation to get the actual paths from conan and afterwards using a combination of grep, sed and jq to shove those paths into ```c_cpp_properties.json```.

Here ist the ```install.sh``` script I came up with:

```
mkdir build
cd build
conan install .. --build=missing
echo "\"\${workspaceFolder}/**\"" > ../.vscode/includePaths
conan info --paths .. | grep package_folder | sed -r 's|package_folder: (.*)|\"\1/**\"|g' >> ../.vscode/includePaths
cd ../.vscode
jq ".configurations[].includePath = []" c_cpp_properties.json > mod.json
while read p; do
jq ".configurations[].includePath += [$p]" mod.json > modx.json
cat modx.json > mod.json
done < includePaths
cat mod.json > c_cpp_properties.json
rm mod.json modx.json includePaths
cd ..
```

# GCC-11

Intellisense was quite happy with just accepting that I wanted ```"cppStandard": "c++20"``` - I wrote it into c_cpp_properties.json and that was ist. 
```CMakeLists.txt``` also needed a change: ```add_definitions("-std=c++2a")```.

Now that was easy but actually things turned on me pretty fast. I knew the compiler was not able to compile c++20 and I wanted to see the error messages. Depending on what part of the snippet I commented out I got one of these two messages:

```
fatal error: compare: No such file or directory

error: expected primary-expression before ‘>’ token
```

compare is a library that would come with a new installation and the starship operator would then also be recognized.

It turns out that the image is a debian image I could not get it to nr an ubuntu image so I wrote my own. Later I found out that my arg entry was overridden by ```devcontainer.json``` maybe I would go down that road next time.

After fiddeling around with what felt like ages I was able to come up with a new dockerfile that fits my needs.

```
FROM mcr.microsoft.com/vscode/devcontainers/base:ubuntu
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update
RUN sudo apt-get -y install software-properties-common
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys CC86BB64
RUN add-apt-repository ppa:ubuntu-toolchain-r/test
RUN add-apt-repository -y ppa:rmescandon/yq
RUN apt-get update
RUN apt-get -y install build-essential cmake cppcheck valgrind clang lldb llvm gdb python3-pip gcc-11 g++-11 yq jq
RUN apt-get autoremove -y
RUN apt-get clean -y
RUN rm -rf /var/lib/apt/lists/*
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 10
RUN update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-11 10
RUN pip3 install conan
ENV CONAN_V2_MODE=1
USER vscode
RUN conan config init
RUN yq w -i ~/.conan/settings.yml 'compiler.gcc.version[+]' '11'
```

After the dockerfile was developed things started to compile pretty quickly using console comands. I created a build.sh script that could build for me. The only issue is taht there is a state where the CMakeCache.txt could have been created without the container and showing incorrect paths - which I fixed manually one and I might look into that in the future.

```
cd build
echo makefiles
cmake .. -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release
#recover strategy would be to remove CMakeCache.txt and retry
echo build
cmake --build .
cd ..
```

After that was done it was time to switch out the executeable in ```.vscode/launch.json``` and add my ```build.sh``` script as the build command in ```tasks.json```.

# Next steps
I still need to add static code validation, debugging, formatting and unit testing and at some point in the future I will create a public repo with everything I wrote as a starting point for future me.

# Things I read while reseaching the topic:

* [conan-bashcompletion](https://gitlab.com/akim.saidani/conan-bashcompletion)
* [conan getting started](https://docs.conan.io/en/latest/getting_started.html)
* [conan center poco](https://conan.io/center/poco)
* [linking concn include to vs code](https://stackoverflow.com/questions/58077908/linking-conan-include-to-vs-code)
* [cpp properties schema reference](https://code.visualstudio.com/docs/cpp/c-cpp-properties-schema-reference)
* [boost locale hello world](https://www.boost.org/doc/libs/1_48_0/libs/locale/doc/html/hello_8cpp-example.html)
* [three way comparison](https://en.cppreference.com/w/cpp/language/operator_comparison#Three-way_comparison)
* [gcc on ubuntu 18.04](https://linuxhostsupport.com/blog/how-to-install-gcc-on-ubuntu-18-04/)
* [gcc on ubuntu 20.04](https://linuxize.com/post/how-to-install-gcc-on-ubuntu-20-04/)
* [add-apt-repository not found](https://phoenixnap.com/kb/add-apt-repository-command-not-found-ubuntu)
* [gcc-10 on ubuntu 18.04](https://askubuntu.com/questions/1192955/how-to-install-g-10-on-ubuntu-18-04)
* [gcc in buster](https://www.reddit.com/r/debian/comments/mmure2/upgrading_the_gcc_gnu_compiler_in_buster/)
* [conan custom settings](https://docs.conan.io/en/1.36/extending/custom_settings.html#custom-settings)

# Commands cheat sheet
```
cat /proc/version
python --version
python3 -V
sudo apt install python3-pip
source ~/.profile
pip3 install conan
conan search poco --remote=conan-center
conan inspect poco/1.10.1
export CONAN_V2_MODE=1
rm ~/.conan/profiles/default
conan config init
conan profile show default
conan install .. --build=missing
cmake .. -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release
conan info --paths .. | grep package_folder
conan search boost --remote=conan-center
cat /etc/*release
conan info --paths .. | grep package_folder
gcc --version
sudo apt-get -y upgrade gcc
sudo apt -y install build-essential
sudo apt-get -y install software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
```