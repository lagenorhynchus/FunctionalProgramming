Further Examples
================

## 例題
文字列のリストを受け取り、文字数が奇数のものだけ取り出して先頭大文字にして昇順にソートしたリストを返す関数odd_namesを考える。

> __PythonのREPLでの実行例__

```python
>>> odd_names(['aries', 'taurus', 'gemini', 'cancer', 'leo', 'virgo',
...     'libra', 'scorpio', 'sagittarius', 'capricorn', 'aquarius', 'pisces'])
['Aries', 'Capricorn', 'Leo', 'Libra', 'Sagittarius', 'Scorpio', 'Virgo']
>>> odd_names(['capricorn'])
['Capricorn']
>>> odd_names([])
[]
```


## 実装例0. ループ
オーソドックスな手続き型スタイルの実装では、ループを利用してリストの要素にアクセスする。

##### Python

```python
# for文によるループ
def odd_names(names):
    res = []
    for name in names:
        if len(name) % 2 != 0:
            res.append(name.capitalize())
    return sorted(res)
```


## 実装例1. 高階関数reduce/fold
手続き型のループに相当する処理は、高階関数reduceを利用して関数型スタイルで実装することができる。

##### Python

```python
# 高階関数reduce
def odd_names1(names):
    def odd_name(res, name):
        if len(name) % 2 != 0:
            res.append(name.capitalize())
        return res
    return sorted(reduce(odd_name, names, []))
```

##### Haskell

```haskell
-- 高階関数foldl'
oddNames1 :: [String] -> [String]
oddNames1 = sort . foldl' oddName []
  where
    capitalize []     = []
    capitalize (c:cs) = toUpper c : map toLower cs
    oddName res name
      | odd $ length name = capitalize name : res
      | otherwise         = res

-- foldl'の引数にラムダ式を利用
oddNames1_2 :: [String] -> [String]
oddNames1_2 = sort . foldl' (\res name ->
  if odd $ length name
    then capitalize name : res
    else res) []
  where
    capitalize []     = []
    capitalize (c:cs) = toUpper c : map toLower cs
```

##### Clojure

```clojure
;; 高階関数reduce
(defn odd-names1 [names]
  (letfn [(odd-name [res name]
            (if (odd? (count name))
              (cons (capitalize name) res)
              res))]
    (sort (reduce odd-name [] names))))

;; reduceの引数にラムダ式を利用
(defn odd-names1_2 [names]
  (sort (reduce (fn [res name]
                  (if (odd? (count name))
                    (cons (capitalize name) res)
                    res))
                [] names)))
```

##### Scala

```scala
// 高階関数foldLeft
def oddNames1(names: List[String]): List[String] = {
  def oddName(res: List[String], name: String) =
    name match {
      case name if name.length % 2 != 0 => name.capitalize :: res
      case _ => res
    }

  names.foldLeft(List[String]())(oddName).sorted
}

// foldLeftの引数にラムダ式を利用
def oddNames1_2(names: List[String]): List[String] =
  names.foldLeft(List[String]())((res, name) =>
    name match {
      case name if name.length % 2 != 0 => name.capitalize :: res
      case _ => res
    }
  ).sorted
```

##### OCaml

```ocaml
(* 高階関数fold_left *)
let odd_names1 names =
  let odd_name res = function
  | name when (String.length name) mod 2 <> 0 ->
    String.capitalize name :: res
  | _ -> res in
  List.sort compare (List.fold_left odd_name [] names)

(* fold_leftの引数にラムダ式を利用 *)
let odd_names1_2 names =
  List.sort compare (List.fold_left (fun res name ->
    match name with
    | name when (String.length name) mod 2 <> 0 ->
      String.capitalize name :: res
    | _ -> res) [] names)
```

##### Erlang

```erlang
%% 高階関数foldl
odd_names1(Names) ->
  Capitalize = fun
    ([]) ->
      [];
    ([C|Cs]) ->
      [string:to_upper(C) | string:to_lower(Cs)]
  end,
  OddName = fun(Name, Res) ->
    case Name of
      Name when length(Name) rem 2 =/= 0 ->
        [Capitalize(Name) | Res];
      _ ->
        Res
    end
  end,
  lists:sort(lists:foldl(OddName, [], Names)).

%% foldlの引数にラムダ式を利用
odd_names1_2(Names) ->
  Capitalize = fun
    ([]) ->
      [];
    ([C|Cs]) ->
      [string:to_upper(C) | string:to_lower(Cs)]
  end,
  lists:sort(lists:foldl(fun(Name, Res) ->
    case Name of
      Name when length(Name) rem 2 =/= 0 ->
        [Capitalize(Name) | Res];
      _ ->
        Res
    end
  end, [], Names)).
```


## 実装例2. 高階関数filterとmap
上記のループや高階関数reduceで実装した処理は、リストの要素の「抽出」と「変換」という2段階に分けて考えると、それぞれ高階関数filter、mapが利用できる。

##### Python

```python
# 高階関数filterとmap
def odd_names2(names):
    def is_odd_len(x):
        return len(x) % 2 != 0
    def capitalize(x):
        return x.capitalize()
    return sorted(map(capitalize, filter(is_odd_len, names)))
```

##### Haskell

```haskell
-- 高階関数filterとmap
oddNames2 :: [String] -> [String]
oddNames2 = sort . map capitalize . filter (odd . length)
  where
    capitalize []     = []
    capitalize (c:cs) = toUpper c : map toLower cs
```

##### Clojure

```clojure
;; 高階関数filterとmap
(defn odd-names2 [names]
  (sort (map #(capitalize %) (filter #(odd? (count %)) names))))

;; マクロ->>を利用
(defn odd-names2_2 [names]
  (->> names (filter #(odd? (count %))) (map #(capitalize %)) sort))
```

##### Scala

```scala
// 高階関数filterとmap
def oddNames2(names: List[String]): List[String] =
  names.filter(_.length % 2 != 0).map(_.capitalize).sorted
```

##### OCaml

```ocaml
(* 高階関数filterとmap *)
let odd_names2 names =
  let is_odd_len x =
    (String.length x) mod 2 <> 0 in
  List.sort compare (List.map String.capitalize (List.filter is_odd_len names))

(* 演算子|>を利用 *)
let odd_names2_2 names =
  let is_odd_len x =
    (String.length x) mod 2 <> 0 in
  names |> List.filter is_odd_len |> List.map String.capitalize |> List.sort compare
```

##### Erlang

```erlang
%% 高階関数filterとmap
odd_names2(Names) ->
  IsOddLen = fun(X) ->
    length(X) rem 2 =/= 0
  end,
  Capitalize = fun
    ([]) ->
      [];
    ([C|Cs]) ->
      [string:to_upper(C) | string:to_lower(Cs)]
  end,
  lists:sort(lists:map(Capitalize, lists:filter(IsOddLen, Names))).
```


## 実装例3. リスト/シーケンス内包表記
高階関数filterとmapの機能は汎用的でよく利用されるため、一部の言語ではリスト内包表記(list comprehension)またはシーケンス内包表記(sequence comprehension)という同等の処理を行うための簡潔な構文が用意されている。

##### Python

```python
# リスト内包表記
def odd_names3(names):
    return sorted([name.capitalize() for name in names if len(name) % 2 != 0])

# ジェネレータ式
def odd_names3_2(names):
    return sorted(name.capitalize() for name in names if len(name) % 2 != 0)
```

##### Haskell

```haskell
-- リスト内包表記
oddNames3 :: [String] -> [String]
oddNames3 names = sort [capitalize name | name <- names, odd $ length name]
  where
    capitalize []     = []
    capitalize (c:cs) = toUpper c : map toLower cs
```

##### Clojure

```clojure
;; シーケンス内包表記
(defn odd-names3 [names]
  (sort (for [name names :when (odd? (count name))] (capitalize name))))
```

##### Scala

```scala
// シーケンス内包表記
def oddNames3(names: List[String]): List[String] =
  (for (name <- names if name.length % 2 != 0) yield name.capitalize).sorted
```

##### OCaml

```ocaml
(* 標準機能にはない…… *)
```

##### Erlang

```erlang
%% リスト内包表記
odd_names3(Names) ->
  IsOddLen = fun(X) ->
    length(X) rem 2 =/= 0
  end,
  Capitalize = fun
    ([]) ->
      [];
    ([C|Cs]) ->
      [string:to_upper(C) | string:to_lower(Cs)]
  end,
  lists:sort([Capitalize(Name) || Name <- Names, IsOddLen(Name)]).
```
