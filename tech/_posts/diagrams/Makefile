IMAGEDIR=../../images
DOTS = $(wildcard *.dot)
PNGS = $(patsubst %.dot,%.png,$(DOTS))

all: $(PNGS)

%.png: %.dot
	dot -Tpng -o$(IMAGEDIR)/$@ $<
