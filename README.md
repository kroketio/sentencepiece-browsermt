# SentencePiece

Fork of [browsermt/sentencepiece](https://github.com/browsermt/sentencepiece) made for [kotki](https://github.com/kroketio/kotki).

### as system lib

```bash
cmake -DCMAKE_BUILD_TYPE=Release -DSPM_BUILD_LIBRARY_ONLY=ON -Bbuild .
make -Cbuild -j6
sudo make install
```

Also writes a CMake config and pkgconfig.
