# ipycomms

This module is a thin wrapper around the `ipykernel.comms` functionality, providing lightweight bidirectional messaging between Python in an IPython (Jupyter) notebook and Javascript running in the notebook output.

Messaging is oriented around topics (arbitrary strings) and doesn't require establishing a channel before sending / receiving messages. Messages on unhandled topics are ignored. 

This is a fairly low-level way to get Javascript to work with your notebook. You should see if [`IPython.notebook.kernel.execute`](https://jakevdp.github.io/blog/2013/06/01/ipython-notebook-javascript-python-communication/) or [widgets / `interact`](https://github.com/ipython/ipywidgets/blob/master/examples/notebooks/Index.ipynb) would better suit your needs.

## Installation

`pip install ipycomms`

## Usage

In your notebook, on the Python side:

```
# This will setup kernel-side handlers and inject some Javascript into your notebook.
import ipycomms

# A handler which will echo back messages.
def print_message(msg):
    # (Useful trick -- if you want to print things in handler functions normal stdout / notebook output
    #  won't do the trick. The easiest thing I've found is to steal the kernel logger, which goes to
    #  the IPython kernel stdout.)
    get_ipython().kernel.log.warn(msg)
    
    ipycomms.send('some.topic', msg)

# Register a listener
ipycomms.setListener('some.topic', print_message)
```

Javascript (this example is for a notebook cell -- in practice it would probably be used somewhere in injected code):

```
%%javascript
// Print messages sent to "some.topic"
IPython.ipycomms.setListener("some.topic", function(data){
    console.log(data)
})

// Send a message
IPython.ipycomms.send("some.topic", [1,2,3])
```
