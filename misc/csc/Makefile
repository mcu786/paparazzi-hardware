#   Paparazzi main $Id: Makefile,v 1.13 2007/03/27 16:12:22 poine Exp $
#   Copyright (C) 2004-2005 Pascal Brisset Antoine Drouin
#
# This file is part of paparazzi.
#
# paparazzi is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2, or (at your option)
# any later version.
#
# paparazzi is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with paparazzi; see the file COPYING.  If not, write to
# the Free Software Foundation, 59 Temple Place - Suite 330,
# Boston, MA 02111-1307, USA.  

GERBV = gerbv


BRD_SRCS = \
    csc.brd \

DIRS = .

##### This substitutions work only with EXACTLY ONE level of subdirectory !!!!!
SCHEMATICS=$(subst /,/printable/,$(BRD_SRCS:%.brd=%.schematics.ps))
TOP_LAYER=$(subst /,/printable/,$(BRD_SRCS:%.brd=%.top_layer.ps))
TOP_COMP=$(subst /,/printable/,$(BRD_SRCS:%.brd=%.top_comp.ps))
BOTTOM_LAYER=$(subst /,/printable/,$(BRD_SRCS:%.brd=%.bottom_layer.ps))
BOTTOM_COMP=$(subst /,/printable/,$(BRD_SRCS:%.brd=%.bottom_comp.ps))
POSTSCRIPTS= $(SCHEMATICS) $(TOP_LAYER) $(TOP_COMP) $(BOTTOM_LAYER) $(BOTTOM_COMP)
PDF=$(POSTSCRIPTS:%.ps=%.pdf)


GERB_O = $(BRD_SRCS:.brd=.all)

all: gerber

_view: all
#	$(GERBV) csc.stc csc.plc csc.cmp csc.l02 csc.l15 csc.sts csc.sol
	$(GERBV) -p csc.gerbv

gerber: $(GERB_O)

printable: dirs $(POSTSCRIPTS) $(PDF)

dirs :
	mkdir -p $(DIRS:%=%/printable)


### GERBER GENERATION

.PRECIOUS : %.cmp %.sol %.stc %.sts %.plc %.bor %.xln %.l02 %.l15

%.all: %.cmp %.sol %.stc %.sts %.plc %.xln %.bor %.l02 %.l15
	touch $@

# Component side
%.cmp : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< Top Pads Vias
# Solder side
%.sol : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< Bottom Pads Vias
# Solder stop mask comp. side
%.stc : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< tStop
# Solder stop mask sold. side
%.sts : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< bStop
# Silkscreen comp. side
%.plc : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< Dimension tDocu
# Drill data for NC drill st.
# warning : eagle takes path of -R option from input file directory.
%.xln : %.brd
	eagle -X -N -d EXCELLON -E -0.02 -E 0.1 -R fab/eurocircuits.drl -o $@ $< Drills Holes
# Outline
%.bor : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< Dimension
# Inner layer top
%.l02 : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< Route2 Pads Vias
# Inner layer bot
%.l15 : %.brd
	eagle -X -N -d GERBER_RS274X -o $@ $< Route15 Pads Vias


### POSTSCRIPT AND PDF GENERATION


%.pdf : %.ps 
	ps2pdf $< $@

%.schematics.ps : ../%.sch
	eagle -X -N -d PS -x.4 -y.2 -o $@ $< Nets Busses Symbols Names Values 

%.top_layer.ps : ../%.brd
	eagle -X -N -f- -m+ -d PS -h2 -w3 -o $@ $< Top Pad Via Dimension 

%.top_comp.ps : ../%.brd
	eagle -X -N -f- -d PS -h2 -w2 -o $@ $< Pad Via Dimension tPlace tNames tDocu

%.bottom_layer.ps : ../%.brd
	eagle -X -N -f- -d PS -h2 -w3 -o $@ $< Bottom Pad Via Dimension 

%.bottom_comp.ps : ../%.brd
	eagle -X -N -f- -m+ -c -d PS -h2 -w2 -o $@ $< Pad Via Dimension bPlace bNames bDocu

clean:
# gerber and excellon
	find .. -name '*.cmp' -exec rm -f {} \; 
	find .. -name '*.sol' -exec rm -f {} \; 
	find .. -name '*.stc' -exec rm -f {} \; 
	find .. -name '*.sts' -exec rm -f {} \; 
	find .. -name '*.plc' -exec rm -f {} \; 
	find .. -name '*.bor' -exec rm -f {} \; 
	find .. -name '*.xln' -exec rm -f {} \; 
	find .. -name '*.dri' -exec rm -f {} \; 
	find .. -name '*.gpi' -exec rm -f {} \; 
	find .. -name '*.l02' -exec rm -f {} \; 
	find .. -name '*.l15' -exec rm -f {} \; 
	find .. -name '*.all' -exec rm -f {} \; 
# printable
	find . -name '*.schematics.ps' -exec rm -f {} \; 
	find . -name '*.schematics.pdf' -exec rm -f {} \; 
	find . -name '*.top_layer.ps' -exec rm -f {} \; 
	find . -name '*.top_layer.pdf' -exec rm -f {} \; 
	find . -name '*.top_comp.ps' -exec rm -f {} \; 
	find . -name '*.top_comp.pdf' -exec rm -f {} \; 
	find . -name '*.bottom_layer.ps' -exec rm -f {} \; 
	find . -name '*.bottom_layer.pdf' -exec rm -f {} \; 
	find . -name '*.bottom_comp.ps' -exec rm -f {} \; 
	find . -name '*.bottom_comp.pdf' -exec rm -f {} \; 
# eagle error log
	find .. -name '*.erc' -exec rm -f {} \; 
# eagle autorouter log
	find .. -name '*.pro' -exec rm -f {} \; 
# eagle backups	
	find .. -name '*#*' -exec rm -f {} \; 
# emacs backups
	find .. -name '*~' -exec rm -f {} \; 
