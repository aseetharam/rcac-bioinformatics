---
authors:
- person_a
---

🛠️ **Prerequisites**




### Heading Level 3


Note, that headings that are not at the root level of the document will not be included in the table of contents. Using the attrs_block extension, you can also add classes to headings




{.bg-primary}
### Paragraph heading


:::{attention}
Let's give readers a helpful hint!
:::

:::{caution}
Let's give readers a helpful hint!
:::


:::{warning}
Let's give readers a helpful hint!
:::



:::{danger}
Let's give readers a helpful hint!
:::



:::{error}
Let's give readers a helpful hint!
:::


:::{hint}
Let's give readers a helpful hint!
:::


:::{important}
Let's give readers a helpful hint!
:::



:::{seealso}
Let's give readers a helpful hint!
:::


:::{note}
Let's give readers a helpful hint!
:::


:::{tip}
Let's give readers a helpful hint!
:::


:::{versionadded} 1.2.3
Explanation of the new feature.
:::

:::{versionchanged} 1.2.3
Explanation of the change.
:::

:::{deprecated} 1.2.3
Explanation of the deprecation.
:::


:::{admonition} My custom title with *Markdown*!
:class: tip

This is a custom title for a tip admonition.
:::



:::{note}
:class: dropdown

This admonition has been collapsed,
meaning you can add longer form content here,
without it taking up too much space on the page.
:::



:::bg-primary
This is a container with a custom CSS class.

- It can contain multiple blocks
:::



:::{card} Card Title
Header
^^^
Card content
+++
Footer
:::



::::{tab-set}

:::{tab-item} Label1
Content 1
:::

:::{tab-item} Label2
Content 2
:::

::::



:::{table} Table caption
:widths: auto
:align: center

| foo | bar |
| --- | --- |
| baz | bim |
:::



```{list-table} Frozen Delights!
:widths: 15 10 30
:header-rows: 1

*   - Treat
    - Quantity
    - Description
*   - Albatross
    - 2.99
    - On a stick!
*   - Crunchy Frog
    - 1.49
    - If we took the bones out, it wouldn't be
 crunchy, now would it?
*   - Gannet Ripple
    - 1.99
    - On a stick!
```

:::{list-table} Frozen Delights!
:widths: 15 10 30
:header-rows: 1

*   - Treat
    - Quantity
    - Description
*   - Albatross
    - 2.99
    - On a stick!
*   - Crunchy Frog
    - 1.49
    - If we took the bones out, it wouldn't be
 crunchy, now would it?
*   - Gannet Ripple
    - 1.99
    - On a stick!
:::



```{csv-table} Frozen Delights!
:header: >
:    "Treat", "Quantity", "Description"
:widths: 15, 10, 30

"Albatross", 2.99, "On a stick!"
"Crunchy Frog", 1.49, "If we took the bones out, it wouldn't be crunchy, now would it?"
"Gannet Ripple", 1.99, "On a stick!"
```




```{code-block} python
:caption: This is a caption
:emphasize-lines: 2,3
:lineno-start: 1

a = 1
b = 2
c = 3
```


```{literalinclude} assets/scripts/example.py
```



Since Pythagoras, we know that {math}`a^2 + b^2 = c^2`.

```{math}
:label: mymath
(a + b)^2 = a^2 + 2ab + b^2

(a + b)^2  &=  (a + b)(a + b) \\
           &=  a^2 + 2ab + b^2
```

The equation {eq}`mymath` is a quadratic equation.
