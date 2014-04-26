===================================================
Cello: Higher level programming in C (libcello.org)
===================================================

.. toctree::
  :maxdepth: 1

  documentation/index
  contribute/index

Celloは、C言語に対してより高次なプログラミングを取り入れるライブラリです。

* **インタフェース** により、構造化された設計が可能となります。
* **ダックタイピング** により、ジェネリックな関数を実現することができます。
* **例外** を用いてエラーハンドリングを行うことができます。
* **コンストラクタ/デストラクタ** によりメモリ管理の問題を解消します。
* **シンタックスシュガー** により可読性が向上します。
* **Cのライブラリ** なので、性能と統合性は最高です。

.. code-block:: c

  /* Example libCello Program */
  
  #include "Cello.h"
  
  int main(int argc, char** argv) {
    
    /* Stack objects are created using "$" */
    var int_item = $(Int, 5);
    var float_item = $(Real, 2.4);
    var string_item = $(String, "Hello");
    
    /* Heap objects are created using "new" */
    var items = new(List, int_item, float_item, string_item);
    
    /* Collections can be looped over */
    foreach (item in items) {
      /* Types are also objects */
      var type = type_of(item);
      print("Object %$ has type %$\n", item, type);
    }
    
    /* Heap objects destroyed with "delete" */
    delete(items); 
  }


クイックスタート
================

より多くの例が、ドキュメントのクイックスタート部にあります。そちらも御覧ください。

.. toctree::
  :maxdepth: 1

  documentation/containers
  documentation/types
  documentation/exceptions
  documentation/functions
  documentation/memory

また、Celloを創ったことについての記事もあります。

.. toctree::
  :maxdepth: 1

  documentation/hacking
  documentation/comparison

上記よりもより長い記述例を示します。

.. code-block:: c

  /* Another Example Cello Program */
  
  #include "Cello.h"
  
  int main(int argc, char** argv) {
  
    /* Tables require "Eq" and "Hash" on key type */
    var prices = new(Table, String, Int);
    put(prices, $(String, "Apple"),  $(Int, 12)); 
    put(prices, $(String, "Banana"), $(Int,  6)); 
    put(prices, $(String, "Pear"),   $(Int, 55));
  
    /* Tables also supports iteration */
    foreach (key in prices) {
      var price = get(prices, key);
      print("Price of %$ is %$\n", key, price);
    }
  
    /* "with" automatically closes file at end of scope. */
    with (file in open($(File, NULL), "prices.bin", "wb")) {
  
      /* First class function object */
      lambda(write_pair, args) {
  
        /* Run time type-checking with "cast" */
        var key = cast(at(args, 0), String);
        var val = cast(get(prices, key), Int);
  
        try {
          print_to(file, 0, "%$ :: %$\n", key, val);
        } catch (e in IOError) {
          println("Could not write to file - got %$", e);
        }
  
        return None;
      };
  
      /* Higher order functions */
      map(prices, write_pair);
    }
  
    delete(prices);
  }


インスピレーション
==================

高次な構造であるCelloプロジェクトは、 *Haskell* から着想を得ています。
また、同時に文法と意味論については *Python* と *Objective-C* に由来します。
CelloはCにおけるオブジェクト指向ではありません。
CelloはCを *動的* で *強力* な関数型言語へと変貌させるツールを提供します。
Celloが示すこの導線が認められる現実というものが、あっても良いのではないでしょうか。

Celloの文法は心地よいものになっていますが、Celloは初心者向けのライブラリ *ではありません* 。
これはCのパワーユーザーのためのものです。
手動でのメモリ管理は、多くの高次な概念とは相性が悪いからです。
結局のところ、CelloはCが極限までハックされるとどのようになるかを見てみるための楽しい実験なのです。


コントリビューション
====================

CelloはBSD3ライセンスです。
`GitHub <https://github.com/orangeduck/libCello>`_ で貢献して下さい。歓迎します。
問い合わせはFreenodeのIRCチャンネル ``#libcello`` にてお願いします。
もしくは、 ``contact@theorangeduck.com`` までメールして下さい。

