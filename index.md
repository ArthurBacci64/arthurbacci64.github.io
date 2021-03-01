# Atr

<img src="https://hitcounter.pythonanywhere.com/count/tag.svg?url=https%3A%2F%2Farthurbacci64.github.io%2F">

## My projects

These are some of my programming projects, most part, if not all, of them are made in C. The command for compiling and running is usually the same:

```
gcc *.c -o main
./main
```

If a different command is needed, it probally will be specified, if it is not, please send a issue to [this repo](https://github.com/arthurbacci64/arthurbacci64.github.io). Thanks.

### 1st: [Teditor, a text editor made with ncurses](https://github.com/ArthurBacci64/Teditor)

Follow the instructions in the `README.md` to compile and run

This code only works in \*NIX operating systems (GNU/Linux, BSDs, MacOS...).

### 2nd: [Discrete Fourier Transform and Fast Fourier Transform](https://github.com/ArthurBacci64/dft-and-fft)

It takes a signal and returns it's Discrete Fourier Transform. I implemented to methods: the "normal" Discrete Fourier Transform and the Fast Fourier Tranform, a really faster version of it.

**You need to compile with this command**:

You will need to compile with `gcc *.c -lm -lncurses -o main` instead of `gcc *.c -o main`. The entire command for compiling and running looks like the following:

```
gcc *.c -lm -lncurses -o main
./main
```

Unfortunaly, compiling with ncurses require a \*NIX operating system (Like GNU/Linux, BSDs... and MacOS too) and ncurses installed. If you do not want to compile for ncurses or can not, follow the steps in the `README.md` file, so you cann compile using only `-lm`.

### I will add more projects to here soon

## My posts

Some posts about programming, GNU/Linux customization and that sort of things.

{% for post in site.posts %}
### [{{ post.title }}]({{ post.url }})

{{ post.excerpt }}

{% endfor %}

