This is just document of libstoragemgmt which code is located at:

    https://github.com/libstorage/libstoragemgmt/

All the markdown files are written in [GitHub Flavored Markdown][1]
syntax.

Meanwhile, in order to provide consistent code layout,
please try to add these lines to your `$HOME/.vimrc`:

```vim
"Treat *.md file as 'markdown' file type.
au BufRead,BufNewFile *.md set filetype=markdown

" Automatically wrap the long line
autocmd Filetype markdown set wrap

" Enable spell check
autocmd Filetype markdown set spell

" Set text width as 72.
autocmd Filetype markdown set tw=72

" If in vim 7.3+, set color column at 72.
if version >=703
    autocmd Filetype markdown set cc=72
fi
```

Please refer to [Github Help:Using Jekyll with Pages][2] for how
to run the website locally for debugging.

[1]: https://help.github.com/articles/github-flavored-markdown/
[2]: https://help.github.com/articles/using-jekyll-with-pages/
