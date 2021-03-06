.. index:: lexical format
.. _text-lexical:

Lexical Format
--------------


.. index:: ! character, Unicode, ASCII, code point, ! source text
   pair: text format; character
.. _source:
.. _text-char:

Characters
~~~~~~~~~~

The text format assigns meaning to *source text*, which consists of a sequence of *characters*.
Characters are assumed to be represented as valid `Unicode <http://www.unicode.org/versions/latest/>`_ *code points*.

.. math::
   \begin{array}{llll}
   \production{character} & \Tchar &::=&
     \unicode{00} ~|~ \dots ~|~ \unicode{D7FF} ~|~ \unicode{E000} ~|~ \dots ~|~ \unicode{10FFFF} \\
   \end{array}

.. note::
   While source text may contain any Unicode character in :ref:`comments <text-comment>` or :ref:`string <text-string>` literals,
   the rest of the grammar is formed exclusively from the characters supported by the `7-bit ASCII <http://webstore.ansi.org/RecordDetail.aspx?sku=INCITS+4-1986%5bR2012%5d>`_ subset of Unicode.


.. index:: ! token, ! keyword, character, white space, comment, source text
   single: text format; token
.. _text-keyword:
.. _text-reserved:
.. _text-token:

Tokens
~~~~~~

The character stream in the source text is divided, from left to right, into a sequence of *tokens*, as defined by the following grammar.

.. math::
   \begin{array}{llll}
   \production{token} & \Ttoken &::=&
     \Tkeyword ~|~ \TuN ~|~ \TsN ~|~ \TfN ~|~ \Tstring ~|~ \Tid ~|~
     \text{(} ~|~ \text{)} ~|~ \Treserved \\
   \production{keyword} & \Tkeyword &::=&
     \mbox{(any terminal symbol in the grammar that is none of the above)} \\
   \production{reserved} & \Treserved &::=&
     \Tidchar^+ \\
   \end{array}

In this specification, the set of valid *keyword* tokens is defined implicitly, by their occurrence as a terminal symbols in literal form in the non-lexical grammar.
Any sequence of alphanumeric or symbolic characters that is not in the other classes is considered a *reserved word*.

Tokens are formed from the input character stream according to the *longest match* rule.
That is, the next token always consists of the longest possible sequence of characters that is recognized by the above grammar.
Where necessary, tokens may be separated by :ref:`white space <text-space>`.


.. index:: ! white space, character, ASCII
   single: text format; white space
.. _text-format:
.. _text-space:

White Space
~~~~~~~~~~~

*White space* is any sequence of literal space characters, formatting characters, or :ref:`comments <text-comment>`.
The allowed formatting characters correspond to a subset of the `ASCII <http://webstore.ansi.org/RecordDetail.aspx?sku=INCITS+4-1986%5bR2012%5d>`_ *format effectors*, namely, *horizontal tabulation* (:math:`\unicode{09}`), *line feed* (:math:`\unicode{0A}`), and *carriage return* (:math:`\unicode{0D}`).

.. math::
   \begin{array}{llclll@{\qquad\qquad}l}
   \production{white space} & \Tspace &::=&
     (\text{~~} ~|~ \Tformat ~|~ \Tcomment)^\ast \\
   \production{format} & \Tformat &::=&
     \unicode{09} ~|~ \unicode{0A} ~|~ \unicode{0D} \\
   \end{array}

The only relevance of white space is to separate :ref:`tokens <text-token>`, it is ignored otherwise.


.. index:: ! comment, character
   single: text format; comment
.. _text-comment:

Comments
~~~~~~~~

A *comment* can either be a *line comment*, started with a double semicolon :math:`\Tcommentd` and extending to the end of the line,
or a *block comment*, enclosed in delimiters :math:`\Tcommentl \dots \Tcommentr`.
Block comments can be nested.

.. math::
   \begin{array}{llclll@{\qquad\qquad}l}
   \production{comment} & \Tcomment &::=&
     \Tlinecomment ~|~ \Tblockcomment \\
   \production{line comment} & \Tlinecomment &::=&
     \Tcommentd~~\Tlinechar^\ast~~(\unicode{0A} ~|~ \T{eof}) \\
   \production{line character} & \Tlinechar &::=&
     c{:}\Tchar & (c \neq \unicode{0A}) \\
   \production{block comment} & \Tblockcomment &::=&
     \Tcommentl~~\Tblockchar^\ast~~\Tcommentr \\
   \production{block character} & \Tblockchar &::=&
     c{:}\Tchar & (c \neq \text{;} \wedge c \neq \text{(}) \\ &&|&
     \text{;} & (\mbox{the next character is not}~\text{)}) \\ &&|&
     \text{(} & (\mbox{the next character is not}~\text{;}) \\ &&|&
     \Tblockcomment \\
   \end{array}

Here, the pseudo token :math:`\T{eof}` indicates the end of the input.
The *look-ahead* restrictions on the productions for |Tblockchar| disambiguate the grammar such that only well-bracketed uses of block comment delimiters are allowed.

.. note::
   Any formatting and control characters are allowed inside comments.
