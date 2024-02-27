SOURCES ?= $(wildcard *.pdf)

%.png: %.pdf
	convert -density 400 $< $@

all: $(SOURCES:%.pdf=%.png)
