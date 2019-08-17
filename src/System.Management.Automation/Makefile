
RESX2SR=mono $(WINE_MONO_SRCDIR)/mono/mcs/class/lib/build-linux/resx2sr.exe

COMPILE_LIB=csc -target:library

LIB_FLAGS=-unsafe -delaysign+ -keyfile:../../winfx.pub -langversion:8.0 -nostdlib -noconfig -lib:$(MONO_PREFIX)/lib/mono/4.5-api

LIB_REFS=-r:mscorlib.dll -r:System.dll -r:System.Xml.dll -r:System.Data.dll -r:System.DirectoryServices.dll -r:System.Numerics.dll -r:System.Core.dll -r:$(WINE_MONO_SRCDIR)/MMI/src/Microsoft.Management.Infrastructure/Microsoft.Management.Infrastructure.dll

LIB_SRCS=$(shell cat sources.list)

all: System.Management.Automation.dll

%.resources.cs: %.resx
	$(RESX2SR) $< > $@ || rm -f $@

%.resources: %.resx
	resgen $< $@

EMBEDDED_RESOURCES=$(shell cat embeddedresources.list)

embeddedresources.flags: embeddedresources.list
	for i in `cat embeddedresources.list`; do printf -- '-resource:%s,%s\n' $$i `echo $$i|sed "s:/:.:g"`; done > $@ || rm -f $@

# dependency brings in source list and the sources without knowing the sources
json.list: $(WINE_MONO_SRCDIR)/mono/mcs/class/lib/net_4_x/System.Net.Http.Formatting.dll
	cat ../../../mono/mcs/class/System.Net.Http.Formatting/System.Net.Http.Formatting.dll.sources|grep 'external/Newtonsoft\.Json'|sed 's"\.\./\.\./\.\."$(WINE_MONO_SRCDIR)/mono"' > json.list

System.Management.Automation.dll: sources.list $(LIB_SRCS) embeddedresources.flags $(EMBEDDED_RESOURCES) json.list
	$(COMPILE_LIB) $(LIB_FLAGS) $(LIB_REFS) -out:$@ @sources.list @json.list @embeddedresources.flags
	sn -R $@ ../../mono.snk

