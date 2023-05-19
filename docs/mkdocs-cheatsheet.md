### Collapsible blocks

This is just a collection of most of the things I use in MkDocs, feel free to use this as a quick reference, rather than sifting through all the official docs.

When the markdown extension pymdownx.details is enabled and an admonition block is started with `???` instead
of `!!!`, the admonition is rendered as a collapsible block with a small toggle
on the right side:

``` markdown title="Admonition, collapsible"
??? note

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.
```

<div class="result" markdown>

??? note

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.

</div>

Adding a `+` after the `???` token renders the block expanded:

``` markdown title="Admonition, collapsible and initially expanded"
???+ note

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.
```

<div class="result" markdown>

???+ note

    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.

</div>

### Supported types

Following is a list of type qualifiers provided by Material for MkDocs, whereas
the default type, and thus fallback for unknown type qualifiers, is `note`:

=== ":fontawesome-brands-markdown: Markdown Title"
``` markdown
!!! info ":fontawesome-brands-markdown: Markdown Title"
    Info here
```

`note`
!!! note
        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`abstract`, `summary`, `tldr`

!!! abstract

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`info`, `todo`

!!! info

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`tip`, `hint`, `important`

!!! tip

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`success`, `check`, `done`

!!! success

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`question`, `help`, `faq`

!!! question

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`warning`, `caution`, `attention`

!!! warning

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`failure`, `fail`, `missing`

!!! failure

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`danger`, `error`

!!! danger

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`bug`

!!! bug

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`example`

!!! example

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.

`quote`, `cite`

!!! quote

        Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et
        euismod nulla. Curabitur feugiat, tortor non consequat finibus, justo
        purus auctor massa, nec semper lorem quam in massa.


##Icons
`:fontawesome-brands-cloudflare:`= :fontawesome-brands-cloudflare:

`:fontawesome-brands-ubuntu:` = :fontawesome-brands-ubuntu:

`:fontawesome-brands-markdown:` = :fontawesome-brands-markdown:

`:fontawesome-brands-github:` = :fontawesome-brands-github:

`:fontawesome-brands-reddit:` = :fontawesome-brands-reddit:

`:fontawesome-brands-linux:` = :fontawesome-brands-linux:

`:fontawesome-brands-js:` = :fontawesome-brands-js:

`:fontawesome-brands-html5:` = :fontawesome-brands-html5:

`:fontawesome-brands-css3:` = :fontawesome-brands-css3:

`:fontawesome-brands-chrome:` = :fontawesome-brands-chrome:

`:fontawesome-brands-edge:` = :fontawesome-brands-edge:

`:material-bash:` = :material-bash:

`:material-clock-fast:` = :material-clock-fast:

`:material-google-cloud:` = :material-google-cloud:

`:material-terraform:` = :material-terraform:

`:fontawesome-brands-reddit-alien:` = :fontawesome-brands-reddit-alien:

##Tables

=== ":octicons-check-circle-fill-16: Enabled"

    `=== ":octicons-check-circle-fill-16: Enabled"`
    
=== ":octicons-skip-16: Disabled"

    `=== ":octicons-skip-16: Disabled"`
