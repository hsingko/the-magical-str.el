## 原理

使用 text-property 将拼音首字母显示为对应的汉字。

```elisp
;;原始字符串：你好，世界
(setq my-string "nh,sj")

(add-text-properties 0 1 '(display "你") my-string)
(add-text-properties 1 2 '(display "好") my-string)
(add-text-properties 2 3 '(display "，") my-string)
(add-text-properties 3 4 '(display "世") my-string)
(add-text-properties 4 5 '(display "界") my-string)

(insert my-string) ;; 你好，世界
```

在 `table.el` 中，由程序生成了八千多个汉字对应的拼音首字母，然后构成一个名为 `the-magical-hash` 的哈希表 。


## 用法

克隆至本地安装后，程序提供了一个 `get-magical-str` 的方法，可以将中英文混合的字符串进行转换：

```elisp
(get-magical-str "hello,world 你好，世界")

```

生成的结果为：
```
#("hello,world nh，sj" 12 13 (display "你") 13 14 (display "好") 15 16 (display "世") 16 17 (display "界"))#("hello,world nh，sj" 12 13 (display "你") 13 14 (display "好") 15 16 (display "世") 16 17 (display "界"))
```

换句话说就是生成了一个在 emacs 中看上去是“hello,world 你好，世界”的字符串，但实际上它是 "hello,world nh, sj" 

## 用途

可以配合 consult 搜索路径中带有中文的文件，比如下面实现了用拼音首字母搜索 denote 笔记的功能：

```lisp
(defun consult-denote-pinyin ()
  (interactive)
  (find-file (consult--read (mapcar (lambda (fn)
				      (cons (get-magical-str (file-relative-name fn denote-directory))
					    fn))
				    (denote-directory-files))
			    :lookup #'consult--lookup-cdr)))
```


## 性能

目前测试过包含 15000 个文件的文件夹，相应时间大约在 3 秒左右。
