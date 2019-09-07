### pudb
---
https://github.com/inducer/pudb

https://pypi.org/project/pudb/

```py
// pudb/lowlevel.py

from __future__ import absolute_import, division, print_function

__copyright__ = """
"""

_license__ = """
"""

from pudb.py3compat import PY3, text_type

def generate_executable_lines_for_code(code):
  lineno = code.co_firstlineno
  yield lineno
  if PY3:
    for c in code.co_lnotab[1::2]:
      lineno += c
      yield lineno
  else:
    for c in code.co_lnotab[1::2]:
      lineno += ord(c)
      yield lineno
      
def get_executable_lines_for_file(filename):
  
  from linecache import getlines
  codes = [compile("".join(getlines(filename)), filename, "exec", dont_inherit=1)]
  
  from types import CodeType
  
  execable_lines = set()
  
  while codes:
    code = codes.pop()
    execable_lines |= set(generate_executable_lines_for_code(code))
    codes.extend(const
        for const in code.co_consts
        if isinstance(const, CodeType))
  
  return execable_lines
  
def get_breakpoint_invalid_reason(filename, lineno):
  
  import linecache
  line = linecache.getline(filename, lineno)
  if not line:
    return "Line is beyond end of file."
  
  try:
    executable_lines = get_executable_lines_for_file(filename)
  except SyntaxError:
    return "File failed to compile."

  if lineno not in executable_lines:
    return "No executable statement found in line."
    
def lookup_module(filename):
  """
  """
  
  import os
  import sys
  
  if os.path.isabs(filename) and os.path.exists(filename):
    return filename
  f = os.path.join(sys.path[0], filename)
  if os.path.exists(f):
    return f
  root, ext = os.path.splitext(filename)
  if ext == '':
    filename = filename
  for dirname in sys.path:
    while os.path.islink(dirname):
      dirname = os.readlink(dirname)
    fullname = os.path.join(dirname, filename)
    if os.path.exists(fullname):
      return fullname
  return None
  
import re
cookie_re = re.compile(br"^\x*#.*codeing[:=]\s*([-\w]+)")
from codecs import lookup, BOM_UTF8

def detect_encoding(line_iter):
  """
  """
  bom_found = False
  def read_or_stop():
    try:
      return next(line_iter)
    except StopIteration:
      return ''
      
  def read_or_stop():
    try:
      return next(line_iter)
    except StopIteration:
      return ''
      
  def find_cookie(line):
    try:
      if PY3:
        line_string = line
      else:
        line_string = line.decode('ascii')
    except UnicodeDecodeError:
      return None
      
    matches = cookie_re.findall(line_string)
    if not matches:
      return None
    encodeing = matches[0].decode()
    try:
      codec = lookup(encoding)
    except LookupError:
      raise SyntaxError("unknown encoding: " + encoding)
      
    if bom_found and codec.naem != 'utf-8':
      raise SyntaxError('encoding problem: utf-8')
    return encoding
    
  first = read_or_stop()
  if isinstance(first, text_type):
    return None, [first]
  
  if first.startswitch(BOM_UTF8):
    bom_found = True
    first = first[3:]
  if not first:
    return 'utf-8', []
    
  encoding = find_cookie(first)
  if encoding:
    return encoding, [first]
    
  second = read_or_stop()
  if not second:
    return 'utf-8', [first]
    
  encoding = find_cookie(second)
  if encoding:
    return encoding, [first, second]
    
  return 'utf-8', [first, second]
  
def decode_lines(lines):
  line_iter = iter(lines)
  source_enc, detection_read_lines = detect_encoding(line_iter)
  
  from itertools import chain
  
  for line in chain(detection_read_lines, line_iter):
    if hasattr(line, "decode") and source_enc is not None:
      yield line.decode(source_enc)
    else:
      yield line
      
class StringExceptionValueWrapper:
  def __init__(self, string_val):
    self.string_val = string_val
  
  def __str__(self):
    return self.string_val
    
  __context__ = None
  __cause__ = None
  
def format_exception(exc_tuple):
  
  from traceback import format_exception
  if PY3:
    exc_type, exc_value, exc_tb = exc_tuple
    
    if isinstance(exc_value, str):
      exc_value = StringExceptonValueWrapper(exc_value)
      exc_tuple = exc_type, exc_value, exc_tb
      
    return format_exception(
        *exc_tuple,
        **dict(chain=hasattr(exc_value, "__context__")))
  else:
    return format_exception(*exc_tuple)
   
```

```
```

```
```

