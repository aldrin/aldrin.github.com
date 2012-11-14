PELICAN=pelican
PELICANOPTS=
BASEDIR=$(CURDIR)
INPUTDIR=$(BASEDIR)/../../home/web/pages
OUTPUTDIR=$(BASEDIR)/..
CONFFILE=$(BASEDIR)/conf.py

html: clean $(OUTPUTDIR)/index.html
	rm -rf $(OUTPUTDIR)/author
	rm -rf $(OUTPUTDIR)/category
	rm -rf $(OUTPUTDIR)/tags.html
	rm -rf $(OUTPUTDIR)/categories.html

$(OUTPUTDIR)/%.html:
	$(PELICAN) $(INPUTDIR) -o $(OUTPUTDIR) -s $(CONFFILE) $(PELICANOPTS)

clean:
	rm -rf $(OUTPUTDIR)/tag
	rm -rf $(OUTPUTDIR)/feeds
	rm -rf $(OUTPUTDIR)/theme
	rm -rf $(OUTPUTDIR)/static
	rm -rf $(OUTPUTDIR)/author
	rm -rf $(OUTPUTDIR)/category
	rm -f $(OUTPUTDIR)/*.html

regenerate: clean
	$(PELICAN) -r $(INPUTDIR) -o $(OUTPUTDIR) -s $(CONFFILE) $(PELICANOPTS)
