
TEX_FILES=main.tex template.tex titlepage.tex statement.tex sources.tex myreferences.bib

titlepage.pdf: $(TEX_FILES)
	pdflatex -shell-escape template
	bibtex template
	pdflatex -shell-escape template
	pdflatex -shell-escape template
clean:
	rm -f *.aux *.log *.blg *.bbl *.out *.toc
