# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# This makefile compiles the native test cases in the JCK.
# Usage: make build [PLATFORM=name] [SRCDIR=path] [OUTDIR=path] 
# Example:
# make build PLATFORM=win_x86-32   SRCDIR=C:\jck\jck8b\JCK-runtime-8b OUTDIR=C:\jck\jck8b\natives
# make build PLATFORM=linux_x86-32 SRCDIR=/jck/jck8b/JCK-runtime-8b OUTDIR=/jck/jck8b/natives
# make clean PLATFORM=win_x86-32   OUTDIR=C:\jck\jck8b\natives
# make clean PLATFORM=linux_x86-32 OUTDIR=/jck/jck8b/natives

# This makefile needs to run jar.
# If JAVA_HOME is set, use that one.
# If JAVA_HOME is not set, look for jar on the PATH.

WIN=0
ifeq ($(PLATFORM),win_x86-32)
	DESTDIR=$(OUTDIR)\$(PLATFORM)
	WIN=1
endif

ifeq ($(PLATFORM),win_x86-64)
	DESTDIR=$(OUTDIR)\$(PLATFORM)
    WIN=1
endif

# Environment variable OSTYPE is set to cygwin if running under cygwin.
# Set our own macro to indicate we're running under cygwin.
# Also Windows commands will need to be prefixed with cmd /c to run them in a Windows shell
ifndef CYGWIN
  OSTYPE?=$(shell echo $$OSTYPE)
  ifeq ($(OSTYPE),cygwin)
    CYGWIN:=1
  else
    CYGWIN:=0
  endif
endif

D:=/

ifeq ($(WIN),1)
   ifeq (0,$(CYGWIN))
	   D:=\\
   endif
endif 

ifneq (,$(JAVA_HOME))
  JAR:=$(JAVA_HOME)$(D)bin$(D)jar
  $(warning JAVA_HOME set to $(JAVA_HOME). Using $(JAR) to create jar files)
else
  ifneq (,$(findstring win,$(PLATFORM)))
    JAR:=$(dir $(firstword $(shell where jar.exe 2>nul)))
  else  # Unix
    JAR:=$(dir $(firstword $(shell which jar 2>/dev/null)))
  endif
  ifeq (,$(JAR))
    $(error Unable to locate jar from JAVA_HOME or PATH, needed to complete the JCK natives build)
  else
    $(warning Using $(JAR) from PATH to create jar files)
  endif
endif

# Following defaults set for Unix environment. For Windows platform following variables are overriden
LIBPREF=lib
ifneq (,$(findstring aix,$(PLATFORM)))
	LIBEXT=a
else
ifneq (,$(findstring osx,$(PLATFORM)))
	LIBEXT=dylib
else
	LIBEXT=so
endif
endif
ifneq (,$(findstring hp,$(PLATFORM)))
	OFLAG=
	BUILDD=
	OFLAGTEMP=ld -b -o
	JCKJNI=$(OFLAGTEMP) $(FULLOUTDIR)$@ jckjni.o
	#JCKATR=$(OFLAGTEMP) $(FULLOUTDIR)$@ jckatr.o
	JCKJVMTI=$(OFLAGTEMP) $(FULLOUTDIR)$@ jckjvmti.o
	SYSTEMINFOUSENATIVE=$(OFLAGTEMP) $@ com_sun_management_mbeans_loading_SystemInfoUseNativeLib.o
	GETLIBIDFROMNATIVE=$(OFLAGTEMP) $@ com_sun_management_mbeans_loading_GetLibIdFromNativeLib.o
	RANDOMGEN=$(OFLAGTEMP) $(FULLOUTDIR)$@ com_sun_management_mbeans_loading_RandomGen.o
else
	OFLAG=-o
	BUILDD=$(FULLOUTDIR)
	VAR=$@
endif

MKDIR=mkdir -p
CLEANDIR=rm -rf
CLEANFILE=rm -rf
FULLOUTDIR=$(OUTDIR)$(D)$(PLATFORM)$(D)
COPYDIR=cp -r
AND_IF_SUCCESSFUL=&&
export AND_IF_SUCCESSFUL

VPATH=$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jni$(D):$(SRCDIR)$(D)src$(D)share$(D)lib$(D)atr$(D):$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jvmti$(D):$(SRCDIR)$(D)tests$(D)api$(D)javax_management$(D)loading$(D)data$(D)archives$(D)src$(D)C$(D):$(SRCDIR)$(D)src
JMX_DATA_PATH=$(SRCDIR)$(D)tests$(D)api$(D)javax_management$(D)loading$(D)data
SOLARIS_PATH=$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jni$(D)include$(D)solaris
LINUX_PATH=$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jni$(D)include$(D)linux
JNI_INCLUDE_PATH=$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jni$(D)include
JVMTI_INCLUDE_PATH=$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jvmti$(D)include
SRC_PATH=$(SRCDIR)/src

# Following are platform and compiler specific settings
# Prior to jck10 the unix include files are in src/share/lib/jni/include/solaris
# From jck10 the unix include files are in src/share/lib/jni/include/linux

ifeq ($(PLATFORM),linux_x86-32)
	CC=gcc
	CFLAGS=-fPIC -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared 
endif	

ifeq ($(PLATFORM),linux_ppc-32)
	CC=gcc
	CFLAGS=-fPIC -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared 
endif	

ifeq ($(PLATFORM),linux_ppc-64)
	CC=gcc
	CFLAGS=-fPIC -D_LP64 -m64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),linux_ppcle-64)
	CC=gcc
	CFLAGS=-fPIC -D_LP64 -m64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),linux_x86-64)
	CC=gcc
	CFLAGS=-fPIC -D_LP64 -m64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),osx_x86-64)
	CC=gcc
	CFLAGS=-fPIC -D_LP64 -m64 -I$(SRCDIR)/src/share/lib/jni/include/amd64 -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),aix_ppc-32)
	CC=xlc
	CFLAGS=-qstaticinline  -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-G
endif

ifeq ($(PLATFORM),aix_ppc-64)
	CC=xlc
	CFLAGS=-q64 -qstaticinline -D_LP64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-G 
endif

ifeq ($(PLATFORM),linux_390-31)
	CC=gcc
	CFLAGS=-m31 -fPIC -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),linux_390-64)
	CC=gcc
	CFLAGS=-fPIC -D_LP64 -m64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),linux_arm-32)
	CC=gcc
	CFLAGS=-fPIC -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),linux_arm-64)
	CC=gcc
	CFLAGS=-fPIC -D_LP64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
	LDFLAGS=-shared
endif

ifeq ($(PLATFORM),zos_390-31)
	CC=c89
	CFLAGS=-I$(SRCDIR)/src/share/lib/jni/include/unix -I$(SRCDIR)/src/share/lib/jni/include -I$(FULLOUTDIR)/include -I$(SRCDIR)/src -Wc,ASCII -W"c,xplink(backchain,oscall(downstack)),noansialias,float(ieee),langlvl(extended),ROCONST,ROSTRING,enum(4),noupconv,sscom" -W"c,target(zOSV2R2)" -W"c,inline(auto,noreport,1200,8000)" -W"a,goff,ESD" -W"c,dll,expo" -W"l,xplink,compat=min" -Wl,dll -Ds390 -DARCH='"s390"'
	LDFLAGS=-D_ALL_SOURCE -DUSE_MALLOC -DIBM_STACK_GROWS_UP -D_XOPEN_SOURCE_EXTENDED=1 -DIBM_ATOE -DOS390CTRACE -DIBM_WRITE_BARRIER -DIBM_MVS -DASYNCH_FI -DRASTRACE_TDF -DJNI_JCL_H -DIBM_ALL -DIBM_UNIX -D_POSIX_SOURCE -DSOLARIS2 -DIBM_OE_XWINDOWS -DIBM_NOAUDIO -DIBM_OE -DIBM_BUILD_TYPE_int -DJ9J2SE -D_BIG_ENDIAN -D_UNIX03_SOURCE -DSTDC -D_LARGE_FILES 
endif

#Warnings displayed can be ignored
ifeq ($(PLATFORM),zos_390-64)
	CC=c89
	CFLAGS=-I$(SRCDIR)/src/share/lib/jni/include/unix -I$(SRCDIR)/src/share/lib/jni/include -I$(FULLOUTDIR)/include -I$(SRCDIR)/src -Wc,ASCII -W"c,lp64,warn64" -W"l,lp64" -W"c,check(port)" -W"c,xplink(backchain,oscall(downstack)),noansialias,float(ieee),langlvl(extended),ROCONST,ROSTRING,enum(4),noupconv,sscom" -W"c,target(zOSV2R2)" -W"c,inline(auto,noreport,1200,8000)" -W"a,goff,ESD" -W"c,dll,expo" -W"l,xplink,compat=min" -Wl,dll -Ds390 -DARCH='"s390"'
	LDFLAGS=-D_ALL_SOURCE -DUSE_MALLOC -DIBM_STACK_GROWS_UP -D_XOPEN_SOURCE_EXTENDED=1 -DIBM_ATOE -DOS390CTRACE -DIBM_WRITE_BARRIER -DIBM_MVS -DASYNCH_FI -DRASTRACE_TDF -DJNI_JCL_H -DIBM_ALL -DIBM_UNIX -D_POSIX_SOURCE -DSOLARIS2 -DIBM_OE_XWINDOWS -DIBM_NOAUDIO -DIBM_OE -DIBM_BUILD_TYPE_int -DJ9J2SE -D_BIG_ENDIAN -D_UNIX03_SOURCE -DSTDC -D_LARGE_FILES 

endif


ifeq ($(WIN),1)
    ifeq (0,$(CYGWIN))
       SRCDIR := $(subst /,\,$(SRCDIR))
       OUTDIR := $(subst /,\,$(OUTDIR))
       FULLOUTDIR := $(subst /,\,$(FULLOUTDIR))
       # and let's escape backslashes in case one is stripped by the shell
       SRCDIR := $(subst \,\\,$(SRCDIR))
       OUTDIR := $(subst \,\\,$(FULLOUTDIR))
       FULLOUTDIR := $(subst \,\\,$(OUTDIR))
       VPATH := $(subst \,\\,$(VPATH))
       MKDIR=mkdir
       CLEANDIR=rmdir /S /Q
	   CLEANFILE=del /Q /S
	   COPYDIR=xcopy /Q /H /R /E /Y /I
    else 
       # cygwin works with forward slashes
       SRCDIR := $(subst \,/,$(SRCDIR))
       OUTDIR := $(subst \,/,$(OUTDIR))
       FULLOUTDIR := $(subst \,/,$(FULLOUTDIR))
       VPATH := $(subst \,/,$(VPATH))
       JMX_DATA_PATH := $(subst \,/,$(JMX_DATA_PATH))
       # Prefix Windows commands with cmd /c to run them in a Windows shell
       CMD_PREFIX:=cmd /c
    endif

	IFLAGS=/I. /I"$(JAVA_HOME)/../include" /I"$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jni$(D)include$(D)win32" /I"$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jni$(D)include$(D)windows" /I"$(SRCDIR)" /I"$(SRCDIR)$(D)src" /I"$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jni$(D)include" /I"$(SRCDIR)$(D)src$(D)share$(D)lib$(D)jvmti$(D)include"
	CFLAGS=/DWIN32 /D_WINDOWS /LD /MD $(IFLAGS)
	LFLAGS=/NOLOGO /DLL /INCREMENTAL:NO /NODEFAULTLIB:LIBCMTD /OUT:
	LINK_CMD=$(CMD_PREFIX) link
	CC=cl
	LIBPREF=
	LIBEXT=dll
	OFLAG=/Fe
	
	JCKJNI=cd $(FULLOUTDIR) && $(LINK_CMD) $(LFLAGS)"$(FULLOUTDIR)$(D)$@" $(FULLOUTDIR)$(D)jckjni.obj
	JCKJVMTI=cd $(FULLOUTDIR) && $(LINK_CMD) $(LFLAGS)"$(FULLOUTDIR)$(D)$@" $(FULLOUTDIR)$(D)jckjvmti.obj
	SYSTEMINFOUSENATIVE=cd $(FULLOUTDIR) && $(LINK_CMD) $(LFLAGS)"$(FULLOUTDIR)$(D)$@" $(FULLOUTDIR)$(D)com_sun_management_mbeans_loading_SystemInfoUseNativeLib.obj
	GETLIBIDFROMNATIVE=cd $(FULLOUTDIR) && $(LINK_CMD) $(LFLAGS)"$(FULLOUTDIR)$(D)$@" $(FULLOUTDIR)$(D)com_sun_management_mbeans_loading_GetLibIdFromNativeLib.obj
	RANDOMGEN=cd $(FULLOUTDIR) && $(LINK_CMD) $(LFLAGS)"$(FULLOUTDIR)$(D)$@" $(FULLOUTDIR)$(D)com_sun_management_mbeans_loading_RandomGen.obj
endif

ifeq ($(PLATFORM),hp_ux-32)
        CC=cc
        CFLAGS=+DD32 +z -c -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=
endif
ifeq ($(PLATFORM),hp_ux-64)
        CC=cc
        CFLAGS=+DD64 +z -c -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=
endif
ifeq ($(PLATFORM),hp_ia-32)
        CC=cc
        CFLAGS=+DD32 +z -c -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=
endif
ifeq ($(PLATFORM),hp_ia-64)
        CC=cc
        CFLAGS=+DD64 +z -c -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=
endif
ifeq ($(PLATFORM),sol_sparc-32)
        CC=gcc
        CFLAGS=-nodefaultlibs -fPIC -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=-shared
endif
ifeq ($(PLATFORM),sol_sparc-64)
        CC=gcc
        CFLAGS= -m64 -nodefaultlibs -fPIC -R/usr/sfw/lib/64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=-shared
endif
ifeq ($(PLATFORM),sol_x86-32)
        #CC=cc
        #CFLAGS=-G -KPIC -I$(SOLARIS_PATH)
	CC=gcc
	CFLAGS=-G -fPIC -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=
endif
ifeq ($(PLATFORM),sol_x86-64)
        #CC=gcc
	CC=cc
        CFLAGS=-m64 -fPIC -R/usr/sfw/lib/64 -I$(SOLARIS_PATH) -I$(LINUX_PATH) -I$(SRC_PATH)
        LDFLAGS=-shared
endif

OBJS=$(LIBPREF)jckjni.$(LIBEXT)  $(LIBPREF)jckjvmti.$(LIBEXT) $(LIBPREF)systemInfo.$(LIBEXT) $(LIBPREF)jmxlibid.$(LIBEXT) $(LIBPREF)genrandom.$(LIBEXT)

CFLAGS := $(CFLAGS) -I$(SRCDIR) -I$(JNI_INCLUDE_PATH) -I$(JVMTI_INCLUDE_PATH)
.SUFFIXES:.c
	
help:
	@echo "Usage: gmake build PLATFORM=<platform> SRCDIR=<JCK directory> OUTDIR=<target binaries directory>"
	@echo "Requirement : Default compiler and jar tool should be in path"
	@echo platform:	aix_ppc-32
	@echo   aix_ppc-64
	@echo   linux_x86-32
	@echo   linux_x86-64
	@echo   linux_ppc-32
	@echo   linux_ppcle-64
	@echo   linux_ppc-64
	@echo   linux_390-31
	@echo   linux_390-64
	@echo   win_x86-32
	@echo   win_x86-64
	@echo   zos_390-31
	@echo   zos_390-64
	@echo   hp_ux-32
	@echo   hp_ux-64
	@echo   hp_ia-32
	@echo   hp_ia-64
	@echo   sol_sparc-32
	@echo   sol_sparc-64
	@echo   sol_x86-32
	@echo   sol_x86-64
	@echo Binaries are placed in the directory OUTDIR/PLATFORM. 
	@echo Examples:
	@echo "gmake build PLATFORM=win_x86-32 SRCDIR=C:\jck\java8b\JCK-runtime-8b OUTDIR=C:\jck\java8b\natives" 
	@echo "gmake build PLATFORM=linux_x86-32 SRCDIR=/jck/java8b/JCK-runtime-8b OUTDIR=/jck/java8b/natives" 

build:createdir $(OBJS) installjmx

$(LIBPREF)jckjni.$(LIBEXT): jckjni.c 
	cd $(FULLOUTDIR) && $(CC) $(CFLAGS) $(LDFLAGS) $< $(OFLAG)$(FULLOUTDIR)$(VAR)
	$(JCKJNI)

#$(LIBPREF)jckatr.$(LIBEXT): jckatr.c
#	cd $(FULLOUTDIR) && $(CC) $(CFLAGS) $(LDFLAGS) $< $(OFLAG)$(FULLOUTDIR)$(VAR)
#	$(JCKATR)
	
$(LIBPREF)jckjvmti.$(LIBEXT):jckjvmti.c
	cd $(FULLOUTDIR) && $(CC) $(CFLAGS) $(LDFLAGS) $< $(OFLAG)$(FULLOUTDIR)$(VAR)
	$(JCKJVMTI)
	
$(LIBPREF)systemInfo.$(LIBEXT):com_sun_management_mbeans_loading_SystemInfoUseNativeLib.c
	cd $(FULLOUTDIR) && $(CC) $(CFLAGS) $(LDFLAGS) $< $(OFLAG)$(VAR)
	$(SYSTEMINFOUSENATIVE)

$(LIBPREF)jmxlibid.$(LIBEXT):com_sun_management_mbeans_loading_GetLibIdFromNativeLib.c
	cd $(FULLOUTDIR) && $(CC) $(CFLAGS) $(LDFLAGS) $< $(OFLAG)$(VAR)
	$(GETLIBIDFROMNATIVE)

$(LIBPREF)genrandom.$(LIBEXT):com_sun_management_mbeans_loading_RandomGen.c
	cd $(FULLOUTDIR) && $(CC) $(CFLAGS) $(LDFLAGS) $< $(OFLAG)$(FULLOUTDIR)$(VAR)
	$(RANDOMGEN)

createdir:
	$(CLEANDIR) $(FULLOUTDIR)
	$(MKDIR) $(FULLOUTDIR)
	
installjmx:
ifeq ($(WIN),0)
	$(COPYDIR) $(SRCDIR)$(D)tests$(D)api$(D)javax_management$(D)loading$(D)data$(D)* $(FULLOUTDIR)$(D).
	cd $(FULLOUTDIR) && $(JAR) uf $(FULLOUTDIR)/archives/MBeanUseNativeLib.jar $(LIBPREF)systemInfo.$(LIBEXT)
	cd $(FULLOUTDIR) && $(JAR) cf $(FULLOUTDIR)/archives/OnlyLibs.jar $(LIBPREF)jmxlibid.$(LIBEXT)
	$(CLEANFILE) $(FULLOUTDIR)$(D)$(LIBPREF)systemInfo.$(LIBEXT) 
	$(CLEANFILE) $(FULLOUTDIR)$(D)$(LIBPREF)jmxlibid.$(LIBEXT) 
else
	$(COPYDIR) "$(JMX_DATA_PATH)$(D)." "$(FULLOUTDIR)$(D)"
	cd "$(FULLOUTDIR)" && $(JAR) uf "$(FULLOUTDIR)$(D)archives$(D)MBeanUseNativeLib.jar" $(LIBPREF)systemInfo.$(LIBEXT)
	cd "$(FULLOUTDIR)" && $(JAR) cf "$(FULLOUTDIR)$(D)archives$(D)OnlyLibs.jar" $(LIBPREF)jmxlibid.$(LIBEXT)
	$(CLEANFILE) $(FULLOUTDIR)$(D)$(LIBPREF)systemInfo.$(LIBEXT)
	$(CLEANFILE) $(FULLOUTDIR)$(D)$(LIBPREF)jmxlibid.$(LIBEXT)
endif

.PHONY clean:
ifeq ($(WIN),0)
	$(CLEANFILE) *.x *.o *.obj 
	$(CLEANFILE) $(FULLOUTDIR)/*.html
	$(CLEANFILE)  $(FULLOUTDIR)/*.x
	$(CLEANFILE)  $(FULLOUTDIR)/*.o
	$(CLEANFILE)  $(FULLOUTDIR)/*.obj
	$(CLEANFILE)  $(FULLOUTDIR)/*.so
	$(CLEANFILE)  $(FULLOUTDIR)/*.a
	$(CLEANDIR) $(FULLOUTDIR)archives
else
	if exist *.obj $(CLEANFILE) *.obj
	if exist $(FULLOUTDIR)\*.exp $(CLEANFILE) $(subst /,\,$(FULLOUTDIR))\*.exp
	if exist $(FULLOUTDIR)\*.lib $(CLEANFILE) $(subst /,\,$(FULLOUTDIR))\*.lib
	if exist $(FULLOUTDIR)\*.dll $(CLEANFILE) $(subst /,\,$(FULLOUTDIR))\*.dll 
	if exist $(FULLOUTDIR)\*.html $(CLEANFILE) $(subst /,\,$(FULLOUTDIR))\*.html
	if exist $(FULLOUTDIR)\archives $(CLEANDIR) $(subst /,\,$(FULLOUTDIR))\archives	
endif
