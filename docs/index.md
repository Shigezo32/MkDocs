# Welcome to MkDocs

For full documentation visit [mkdocs.org](https://mkdocs.org).

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs build` - Build the documentation site.
* `mkdocs help` - Print this help message.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

```c#
public class User
{
    public string Id { get; set; }
    public int Age { get; set; }
    public int GotLike { get; set; }
    public string NickName { get; set; }
    public TimeSpan SinceLastLoginSeconds { get; set; }
    public DateTimeOffset CreatedAt { get; set; }
}
```

`#!php-inline $a = array("foo" => 0, "bar" => 1);`

<!-- :fa-aaa: github   -->
<!-- :fa-github: github   -->
:fa-camera-retro: camera-retro  

:fa-font-awesome: fontawesome の有効化  

==This was marked==  
^^This was inserted^^  
~~This was deleted~~  

:fontawesome-regular-laugh-wink: aa  
:fontawesome-regular-aaa: aa  

- :fontawesome-brands-medium:{ .medium } – Medium
- :fontawesome-brands-twitter:{ .twitter } – Twitter
- :fontawesome-brands-facebook:{ .facebook } – Facebook

:octicons-heart-fill-24:{ .heart .twitter }

![Placeholder](https://dummyimage.com/600x400/eee/aaa){ loading=lazy }

### H3

#### H4

##### H5

###### H6

```html
<h3 id="Custom-queries">カスタムクエリ</h3>
```

```css
.center {
  display: block; /*ブロック要素にする*/
  margin: 0 auto; /*中心揃え*/
}
```

```c# hl_lines="2 3"
    public class User
    {
        public string Id { get; set; }
        public int Age { get; set; }
        public int GotLike { get; set; }
        public string NickName { get; set; }
        public TimeSpan SinceLastLoginSeconds { get; set; }
        public DateTimeOffset CreatedAt { get; set; }
    }
```

Lorem ipsum[^1] dolor sit amet, consectetur adipiscing elit.[^2]

[^1]: Lorem ipsum dolor sit amet, consectetur adipiscing elit.
[^2]: Lorem ipsum dolor sit amet, consectetur adipiscing elit.

==Mark==

```{.python .extra-class #id linenums="1"}
import hello_world
```

Task List

* [X] item 1
    * [X] item A
    * [ ] item B
        more text
        * [x] item a
        * [ ] item b
        * [x] item c
    * [X] item C
* [ ] item 2
* [ ] item 3

~~取り消し線~~
