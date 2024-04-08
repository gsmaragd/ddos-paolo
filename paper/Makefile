###########################################################
#
# Makefile for LaTeX docs
#

TEX = paper
FIGS = 
PSFIGS = $(FIGS:.pdf=.ps)

all: pdf
ps:  $(TEX).ps
pdf: $(TEX).pdf
dvi: $(TEX).dvi

clean:
	$(RM) *.synctex.gz *.out *.aux *.log *.bbl *.blg *~ \#*\# *.toc *.idx *.eps dummy
	$(RM) $(patsubst %.tex, %.ps, $(wildcard *.tex))
	$(RM) $(patsubst %.tex, %.dvi, $(wildcard *.tex))
	$(RM) $(patsubst %.tex, %.pdf, $(wildcard *.tex))

# only need -Ppdf if using CM fonts

paper.pdf: *.tex *.bib Makefile sections/*.tex
	latexmk -pdf -pdflatex="pdflatex -interaction=nonstopmode" -use-make $<

embed: paper.pdf
	gs -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -dPDFSETTINGS=/prepress -dEmbedAllFonts=true -sOutputFile=paper-embedfonts.pdf paper.pdf

# This is for a tool of mine that monitors files for changes via
# inotify and then executes actions. --cpk
notif:
	notif -vv *.tex *.bib make

source:
	# Needed to create bbl file
	latexmk -pdf -pdflatex="pdflatex -interaction=nonstopmode" -use-make $<
	rm -rf source-to-submit
	mkdir -p source-to-submit
	latexpand --verbose --empty-comments paper.tex > source-to-submit/paper.tex
	cp acmart.cls source-to-submit/
	cp ACM-Reference-Format.bst source-to-submit/
	cp paper.bib source-to-submit/
	cp paper.bbl source-to-submit/
	mkdir -p source-to-submit/figures
	cp $$(egrep '.pdf$$' paper.fls | grep -v paper.pdf | cut -d' ' -f2 | sort -u | egrep '^\.') source-to-submit/figures/
	sed -i -E 's+(.*\\includegraphics.*\{).*/(.*)+\1figures/\2+' source-to-submit/paper.tex
	cp Makefile.minimal source-to-submit/Makefile

arxiv: source
	# Apply arxiv note patch
	patch --no-backup-if-mismatch -p1 source-to-submit/paper.tex < arxiv-note.patch
