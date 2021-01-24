# Atr

## My projects

These are some of my programming projects, most part, if not all, of them are made in C. The command for compiling and running is usually the same:

```
gcc *.c -o main
./main
```

If a different command is needed, it probally will be specified, if it is not, please send a issue to [this repo](https://github.com/arthurbacci64/arthurbacci64.github.io). Thanks.

### [Discrete Fourier Transform and Fast Fourier Transform](https://github.com/ArthurBacci64/dft-and-fft)

At the time I am writing this, that is my most recent project. It basicaly takes a signal and returns it's Discrete Fourier Transform. I implemented to methods: the "normal" Discrete Fourier Transform and the Fast Fourier Tranform, a really faster version of it.

#### You need to compile with this command

You will need to compile with `gcc *.c -lm -lncurses -o main` instead of `gcc *.c -o main`. The entire command for compiling and running looks like the following:

```
gcc *.c -lm -lncurses -o main
./main
```

Unfortunaly, compiling with ncurses require a \*NIX operating system (Like GNU/Linux, BSDs... and MacOS too) and ncurses installed. If you do not want to compile for ncurses or can not, follow the steps in the `README.md` file, so you cann compile using only `-lm`.

### I will add more projects to here soon
