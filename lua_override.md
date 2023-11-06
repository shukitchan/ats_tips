Overriding lua built-in functions
=================================
```
orig_len = string.len
function my_len(s)
  ts.error('len is '..s)
  return orig_len(s)
end

string.len = my_len
abc = 'abc'
ts.error('test len   '..abc:len())
```

We can then put restrictions on input parameters on these functions
