# IDOM - It's React, but in Python

[IDOM](https://github.com/idom-team/idom) is a new declarative Python package for
building highly interactive and composable user interfaces.

![idom logo](https://github.com/idom-team/idom/raw/929d07ff4a643320a6148336613621242284f8d2/docs/source/branding/idom-logo.png)

IDOM takes obvious inspiration from [React](https://reactjs.org/), and wherever
possible, attempts to achieve parity with the features it copies more directly. Nowhere
is this more evident than the version of React's often lauded
["Hooks"](https://reactjs.org/docs/hooks-intro.html) that IDOM implements in Python.

At a glance, the similarities between IDOM and React are rather striking. Below is a
React component which defines a simple `Slideshow` displaying an image that updates when
a user clicks on it, and immediately below it is the same view implemented in Python
using IDOM:

```JSX
import React, { useState } from react;

function Slideshow() {
  const [index, setIndex] = useState(0);
  return (
    <img
      src={ `https://picsum.photos/800/300?image=${index}` }
      onClick={ () => setIndex(index + 1) }
      style={ {cursor: "pointer"} }
    />
  )
}
```

```python
import idom

@idom.component
def Slideshow():
    index, set_index = idom.hooks.use_state(0)
    return idom.html.img(
        {
            "src": f"https://picsum.photos/800/300?image={index}",
            "onClick": lambda event: set_index(index + 1),
            "style": {"cursor": "pointer"},
        }
    )

idom.run(Slideshow)
```


## Why Do We Need IDOM?

Over the past 5 years front-end developers seem to have arrived at the conclusion that
declarative programming is usually better than imperative. Put more simply, mutable
state in programs can quickly lead to unsustainable complexity. This trend is largely
evidenced by [the rise of Javascript
frameworks](https://gist.github.com/tkrotoff/b1caa4c3a185629299ec234d2314e190) like
[Vue](https://vuejs.org/) and [React](https://reactjs.org/) which describe the logic of
computations without explicitly stating their control flow.

![npm download trends](https://gist.githubusercontent.com/rmorshea/24d369fac53c2e1a07557850a0e7ff13/raw/ac4f80fe824d8159b87545ff3632d7da9cf351ce/npm-download-trends.png)

So what does this have to do with Python? Well, because browsers are the de facto
"operating system of the internet", even back-end languages like Python have had to
figure out clever ways to integrate with them. While standard
[REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs were once
well suited to applications built using HTML templates, they don't always enable the
degree of interactivity expected by modern browser users. Ultimately this means the
learning curve for beginners to the Python language is steepened since knowing that
alone isn't enough to build a "modern" full-stack application.

In the wake of these problems various Python packages have sprung up to solve them:

- [IPyWidgets](https://github.com/jupyter-widgets/ipywidgets) - Adds interactive widgets to [Jupyter Notebooks](https://jupyter.org/)
- [Streamlit](https://www.streamlit.io/) - Turns simple Python scripts into interactive dashboards
- [Plotly Dash](https://plotly.com/dash/) - Allows data scientists to produces enterprise-ready analytic apps
- [Bokeh](https://docs.bokeh.org/) - An interactive visualization library for modern web browsers

However they each have drawbacks that can make them difficult to use.

1. **Imperative paradigm** - projects like IPyWidgets and Bokeh do not embrace the
   lessons learned by front-end developers of the afformentioned declarative frameworks.

2. **Limited capabilities** - At their initial inception, the development of these
   libraries was driven by the visualization needs of data scientists, so creating even
   moderately complex UI layouts can be difficult.

3. **Restrictive ecosystems** - UI components developed for one framework cannot be
   easily ported to any of the others because their APIs are either too complex or are
   structurally inaccesible.

A future article will address specific comparisons to each of the projects mentioned
above, but for now we'll just focus on how IDOM avoids the drawbacks listed above.

## Declarative Components

## Composable Layouts

## Ecosystem Independence

## Conclusion

Building highly interactive web applications as a Python developer has historically been
a great challenge. However, IDOM changes that. Knowing just basic HTML, CSS, and Python,
you can make everything from
[slideshows](https://idom-docs.herokuapp.com/docs/examples.html#slideshow) to
[interactive
dashboards](https://idom-docs.herokuapp.com/docs/examples.html#simple-dashboard). Its
usage can be as broad or [targeted](#only-where-you-need-it) as you need it to be, and
you can use it wherever you need it, whether that's in a [Jupyter
notebook](https://github.com/idom-team/idom-jupyter) or standard web application.
