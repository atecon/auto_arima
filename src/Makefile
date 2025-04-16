PKG = auto_arima

$(PKG).gfn: $(PKG).inp $(PKG).spec $(PKG)_sample.inp
	gretlcli --makepkg $(PKG).inp


install: $(PKG).gfn
	echo pkg install $(PKG).gfn --local | gretlcli -b -
	cat $(PKG)_help.md | tee ../README.md

clean:
	rm -f $(PKG).gfn
