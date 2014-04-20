Think about expression of blocks as 1 byte
==========================================

This blog post for my unimportant memo.

I am creating `genaa <https://github.com/hirokiky/genaa/>`_,
a ASCII Art generator.

Now, It can rendering text box like this::

    ┌────────────────────┐
    │Shut the f**k up and│
    │write some code!    │
    └────────────────────┘

And, I am considering about expression of blocks as 1 byte
for providing new rendering feature for genaa.

Free blocks
-------------
The feature is rendering some blocks from sigunature.
How can we express the following block as plain text?::

    ┌─┐
  ┌─┘ └─┐
  └─────┘

(As you know, it's the most gentle block on Tetris)

I noticed the block can be expressed by 6 characters::

    69C
    D47

Each characters mean the lines of the block.
One character is actually hex, and each digits mean corresponding lines::

        1
       ┌─┐
     8 │ │ 2
       └─┘
        4

And the sum of line numbers is the above character.
For instance, 6 means 'bottom' and 'right'::

         │ 2
       ──┘
        4

(Ofcause, this expression may contain some redundancies,
like '28')

This may be powerful and useful to render some blocks.
But, the bad points is hard to read and write as plain text.

.. author:: default
.. categories:: none
.. tags:: genaa,misc
.. comments::
