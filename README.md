### Generating api docs


1. Install luarocks `brew install luarocks`
2. Install ldoc `luarocks install ldoc`
3. Shallow init the submodules that we want to make docs for `git submodule update --init`
3. Run ldoc `ldoc -l _generation -x .md -f markdown _generation/libs`