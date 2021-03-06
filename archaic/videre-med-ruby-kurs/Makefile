# naive makefile for generating the presentation
#
#

RAW_PRES = presentation.sgml
TMP = tmp/
HTML = html/
CODE = code/
PRES = pres.sgml


PROJECT_NAME=rubywebnuby

STANDALONE = standalone_presentation
STANDALONE_SGML = $(addsuffix .sgml, $(STANDALONE))
STANDALONE_PS = $(addsuffix .ps, $(STANDALONE))
STANDALONE_LATEX = $(addsuffix .tex, $(STANDALONE))


EDIT = ruby bin/edit.rb
RB2HTML_RB = bin/rb2htmlz.rb
RB2SGML_RB = bin/rb2sgml.rb
RB2HTML = ruby bin/rb2htmlz.rb
RB2SGML = ruby bin/rb2sgml.rb
RUBY = ruby

FULL_HTML  = $(HTML)flat_$(PRES).html
EMPTY_HTML = $(TMP)flat_*.html
FLAT_PRES   = $(TMP)flat_$(PRES)

EMPTY_ONLINE_HTML = $(TMP)pres*.html


# Source code example source
SAMPLE_CODE_FILES = $(notdir $(wildcard code/*.rb)) \
	$(notdir $(wildcard code/*.rhtml)) \
	$(notdir $(wildcard code/*.rbx)) 

#SAMPLE_CODE_FILES = heiverden1.rb tall1.rb \
	 tekst1.rb	tekst2.rb 	\
	 utput1.rb	variabler1.rb 	\
	 hvis1.rb	hvis2.rb 	\
	 gjenta1.rb	gjenta2.rb 	\
	 iter1.rb 	iter2.rb \
	 metoder1.rb 	

#Same source, restrained to CODE sections
SAMPLE_CODE = $(addprefix html/code/,$(SAMPLE_CODE_FILES))

SAMPLE_CODE_HTML = $(addprefix tmp/,$(addsuffix .html,$(SAMPLE_CODE_FILES)))
SAMPLE_CODE_SGML = $(addprefix tmp/,$(addsuffix .sgml,$(SAMPLE_CODE_FILES)))

SAMPLE_CODE_HTMLSECTIONS = $(addsuffix _section,$(SAMPLE_CODE_HTML))
SAMPLE_CODE_SGMLSECTIONS = $(addsuffix _section,$(SAMPLE_CODE_SGML))

CLEAN_FILES = $(PRES) $(FLAT_PRES) $(EMPTY_HTML) \
	$(SAMPLE_CODE_HTML) $(SAMPLE_CODE_HTMLSECTIONS) $(SAMPLE_CODE_SGMLSECTIONS)\
	$(TMP)*


# Insert code snipppets into HTML
presentation :  $(FULL_HTML)

online : $(SAMPLE_CODE_HTMLSECTIONS) $(EMPTY_ONLINE_HTML)
	$(EDIT) -InsertSampleCode $(EMPTY_ONLINE_HTML)

standalone : $(STANDALONE_SGML) 



ps : $(STANDALONE_PS)
	cp $(STANDALONE_PS) $(HTML)$(PROJECT_NAME).ps

smallps : $(STANDALONE_PS)
	mpage -4 $(STANDALONE_PS) > $(HTML)$(PROJECT_NAME)_4.ps
	mpage -2 $(STANDALONE_PS) > $(HTML)$(PROJECT_NAME)_2.ps

pdf : $(STANDALONE_PS)
	ps2pdf $(STANDALONE_PS) $(HTML)$(PROJECT_NAME).pdf



all : presentation online standalone ps pdf smallps

$(STANDALONE_SGML) : $(PRES) $(SAMPLE_CODE) #$(SAMPLE_CODE_SGMLSECTIONS)
	$(EDIT) -InsertSampleCodeStandalone $(PRES) $(STANDALONE_SGML)
#$(EDIT) -InsertSGMLSampleCode $(PRES) $(STANDALONE_SGML)

$(FULL_HTML) : $(EMPTY_HTML) $(SAMPLE_CODE_HTMLSECTIONS) $(SAMPLE_CODE)
	$(EDIT) -InsertSampleCode $(EMPTY_HTML)

#$(SAMPLE_CODE_HTML) : $(SAMPLE_CODE) 
#	$(EDIT) -Htmlify $(SAMPLE_CODE)


# HTML without source code inserted
$(EMPTY_HTML) : $(FLAT_PRES)
	sgml2html -I $(FLAT_PRES) 
	mv flat_* tmp

$(EMPTY_ONLINE_HTML) : $(PRES)
	sgml2html -I $(PRES)
	mv pres*.html tmp


# flatten the sections so we get slides, not serveral on one page.
$(FLAT_PRES) : $(PRES)
	$(EDIT) -Flatten $(PRES) $(FLAT_PRES)


# Raw input into "real" SGML by doing Umlaut gsub'ing.
$(PRES) : $(RAW_PRES)
	$(EDIT) -Umlaut $< $@

$(STANDALONE_PS) : $(STANDALONE_SGML)
	echo $(STANDALONE_PS)
	sgml2latex --output=ps --charset=latin $(STANDALONE_SGML)

$(STANDALONE_LATEX) : $(STANDALONE_SGML)
	sgml2latex --output=tex --charset=latin $(STANDALONE_SGML)





###################
# Dependancies on Ruby Nuby proper

bin : ../presentation/bin
	ln -s $< $@



#################
# general rules




# Retain only the body part for insertion into remaining doc.
$(TMP)%.rb.html_section : $(TMP)%.rb.html
	$(EDIT) -StripToHtmlSection  $< $@ \
		$(subst .rb.html,.rb,$(subst $(TMP),$(CODE),$<))

# Generate HTML of files
$(TMP)%.rb.html : $(HTML)code/%.rb
	cat $< | $(RB2HTML)  >   $@

$(TMP)%.rb.sgml_section : $(HTML)code/%.rb
	$(RB2SGML) $<  >   $@

# Strip down to contents of <CODE></CODE> tags
$(HTML)code/%.rb : $(CODE)%.rb
	$(EDIT) -StripRbCode $< $@


######### rhtml
$(TMP)%.rhtml.html_section : $(TMP)%.rhtml.html
	$(EDIT) -StripToHtmlSection  $< $@ \
		$(subst .rhtml.html,.rhtml,$(subst $(TMP),$(CODE),$<))

# Generate HTML of files
$(TMP)%.rhtml.html : $(HTML)code/%.rhtml
	cat $< | $(RB2HTML)  >   $@

# Strip down to contents of <CODE></CODE> tags
$(HTML)code/%.rhtml : $(CODE)%.rhtml
	$(EDIT) -StripRbCode $< $@


#########

.PHONY : clean ship shipbase ship flat

clean : 
	rm -f $(CLEAN_FILES) > /dev/null


shipbase:
	-rm -rf ship
	cp -r html ship
	-rm -f `find ship | grep "CVS/"`
	-rmdir  `find ship | grep CVS`
	-rm -f `find ship/figures/*.* | grep -v "\.png" `


ship: shipbase
	-rm -f ship/flat-*
	tar cf ship.tar ship
	gzip ship.tar

flat: shipbase
	-rm -f ship/*.ps ship/*.pdf
	tar cf flat.tar ship
	gzip flat.tar 

