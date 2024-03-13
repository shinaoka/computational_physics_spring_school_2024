SOURCES ?= $(wildcard *.pdf)

%.png: %.pdf
	convert -density 200 $< $@

all: $(SOURCES:%.pdf=%.png)
