PKG = auto_arima

$(PKG).gfn: $(PKG).inp $(PKG).spec $(PKG)_help.txt $(PKG)_sample.inp
	fold -s -w 80 $(PKG)_help.txt | tee $(PKG)_help.txt
	gretlcli --makepkg $(PKG).inp

#format: $(PKG)_help.txt

install: $(PKG).gfn
	echo pkg install $(PKG).gfn --local | gretlcli -b -

clean:
	rm -f $(PKG).zip
