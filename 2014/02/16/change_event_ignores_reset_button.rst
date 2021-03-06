ResetボタンはjQueryのchangeイベントを発生させない
=========================================================

input[type=reset] はクリックしても jQuery の changeイベントを発生させない｡
select要素の変更を検知して何か処理するとして､リセットボタンがクリックされてもその処理は実行されない｡
formのonResetという属性にJSを渡せば､リセットボタンが押された際にその処理を実行できるが､ここでは分離したJSファイル内からイベントを登録することを考える｡

JSファイル内からリセットのイベントにフックするには､そのボタンのクリックイベントにフックすればできる｡ただし､実行させたい処理がリセットによって変更される場合､処理の実行順序を明確にする｡例えば以下のように:

.. code-block:: javascript

    $('input[type=reset]').click(function(e){
        e.preventDefault();
        $(this).closest('form').get(0).reset();

        do_something();
    });

発生したイベントを停止した後､明示的にFormのリセットを行っている｡
実行したい処理が､Formのリセット処理が完了した後に行われることを保証するためである｡

これでリセットボタンでも change イベントと同じようにフックすることができた｡

.. author:: default
.. categories:: none
.. tags:: jquery
.. comments::
