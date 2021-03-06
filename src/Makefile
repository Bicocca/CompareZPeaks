HDR      = ../interface/
OBJ      = ../obj/
LIB      = ../lib/
BIN      =  ../bin/
PRG      =  ../test/
SCRIPT   = ../scripts/


Shell   =  .sh
HdrSuf  =  .h
SrcSuf  =  .cc
ObjSuf  =  .o
PrgSuf  =  .cpp
BinSuf  =  .exe
LibSuf  =  .so

HDRS  =  $(wildcard $(HDR)*$(HdrSuf))
SRCS  =  $(wildcard *$(SrcSuf))
_OBJS  =  $(patsubst %$(SrcSuf), %$(ObjSuf), $(SRCS))
OBJS   =  $(addprefix $(OBJ),$(_OBJS))
PRGS  =  $(wildcard $(PRG)*$(PrgSuf))
_BINS    =  $(wildcard $(PRG)*$(PrgSuf))
__BINS   =  $(_BINS:$(PrgSuf)=$(BinSuf))
___BINS  =  $(notdir $(__BINS))
BINS	 =  $(addprefix $(BIN),${___BINS})

LINKDEF   =  $(wildcard ${HDR}*LinkDef.h)
DICTHDRS  =  $(patsubst $(LINKDEF),,$(HDRS)) $(LINKDEF)


ROOTCFLAGS    = $(shell root-config --cflags)
ROOTGLIBS     = $(shell root-config --glibs)

COMMONUTILSFLAGS = $(shell commonutils-config --include)
COMMONUTILSLIBS = $(shell commonutils-config --libs)

CLHEPCFLAGS   =  $(shell clhep-config --include)
CLHEPLIBS     =  $(shell clhep-config --libs)

ARCHL = -m64

CXX  =  c++
CXXFLAGS  =  -Wall -O -fPIC -I$(HDR) $(COMMONUTILSFLAGS) $(ROOTCFLAGS) -I$(ROOFITSYS)/include
CPP  =  c++
CPPFLAGS  = -Wall $(ARCHL) -I$(HDR) $(COMMONUTILSFLAGS) $(ROOTCFLAGS) -I$(ROOFITSYS)/include

LD       =  c++
LDFLAGS  =  -rdynamic -shared $(ARCHL)
SONAME	 =  libCompareZPeaks.so
SOFLAGS  =  -Wl,-soname,$(SONAME)

GLIBS   =  -lm -ldl -rdynamic $(ROOTGLIBS) $(COMMONUTILSLIBS) -L$(ROOFITSYS)/lib -lGenVector -lFoam -lMinuit -lRooFitCore -lRooFit -lMLP -lXMLIO -lTMVA -lTreePlayer


#################################################
#if mac
ARCH  =  $(shell root-config --arch)
ifeq ($(ARCH),macosx)
LibSuf  =  .dylib

ARCHL = -m32

CPPFLAGS  =  -Wall -W -Woverloaded-virtual -O2 $(ARCHL) -pipe -I$(HDR) $(ROOTCFLAGS) $(COMMONUTILSFLAGS)

CXXFLAGS  =  -Wall -W -Woverloaded-virtual -O2 $(ARCHL) -pipe -I$(HDR) $(ROOTCFLAGS) $(COMMONUTILSFLAGS)

LDFLAGS  =  -dynamiclib -shared -single_module -undefined dynamic_lookup $(ARCHL)
SONAME	 =  libCompareZPeaks.dylib
SOFLAGS  =
endif
#################################################

#################################################
#if mac 64
ARCH  =  $(shell root-config --arch)
ifeq ($(ARCH),macosx64)
LibSuf  =  .dylib

ARCHL = -m64

CPPFLAGS  =  -Wall -W -Woverloaded-virtual -O2 $(ARCHL) -pipe -I$(HDR) $(ROOTCFLAGS) $(COMMONUTILSFLAGS)

CXXFLAGS  =  -Wall -W -Woverloaded-virtual -O2 $(ARCHL) -pipe -I$(HDR) $(ROOTCFLAGS) $(COMMONUTILSFLAGS)

LDFLAGS  =  -dynamiclib -shared -single_module -undefined dynamic_lookup $(ARCHL)
SONAME	 =  libCompareZPeaks.dylib
SOFLAGS  =
endif
#################################################




.PHONY: all exe test clean


all: $(LIB)$(SONAME)


exe: $(BINS) 


test:
	@echo "HDRS = $(HDRS)"
	@echo "DICTHDRS = $(DICTHDRS)"
	@echo "SRCS = $(SRCS)"
	@echo "OBJS = $(OBJS)"
	@echo "PRGS = $(PRGS)"
	@echo "BINS = $(BINS)"


$(OBJ)%$(ObjSuf): %$(SrcSuf) $(HDRS)
	$(CXX) -c $(CXXFLAGS) -o $@ $< 


$(OBJ)mydict.o: $(OBJ)mydict.cc $(OBJ)mydict.h
	$(CXX) -c $(CXXFLAGS) -o $@ $<


$(OBJ)mydict.cc: $(DICTHDRS)
	@echo "Generating dictionary for  ..."
	rootcint -f $(OBJ)mydict.cc -c -p ${CXXFLAGS} $(DICTHDRS)

$(LIB)$(SONAME): $(OBJS) $(OBJ)mydict.o
	@echo "Linking $(SONAME):" 
	$(LD) $(LDFLAGS) $(OBJS) $(OBJ)mydict.o $(SOFLAGS) -o $(LIB)$(SONAME) 

$(BIN)%$(BinSuf): $(PRG)%$(PrgSuf) $(HDRS) $(LIB)$(SONAME)
	$(CPP) $(CPPFLAGS) -L$(LIB) $(GLIBS) -lCompareZPeaks  -o $@ $<


clean:
	rm -f $(BIN)*$(BinSuf) $(OBJ)*$(ObjSuf) $(LIB)*$(LibSuf) $(OBJ)mydict*
