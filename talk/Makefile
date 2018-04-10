.PHONY: clean

NAME = seminar_talk


.PHONY: $(DIRS)

%.pdf:	%.tex
	latexmk -pdf $<

$(NAME).pdf: 

$(DIRS): 
	$(MAKE) -C $@

clean:
	latexmk -C
