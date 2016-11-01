# django-treebeard

This is an efficient implementation of Materialized Path trees for Django, as described by Vadim
Tropashko in SQL Design Patterns. Materialized Path is probably the fastest way of working with
trees in SQL without the need of extra work in the database, like Oracle’s ``CONNECT BY`` or sprocs
and triggers for nested intervals.

For reasons unknown to the community, Gustavo Picón, the maintainer of django-treebeard stopped
to work on this project. The [latest version available from PyPI is 4.0.1](https://pypi.python.org/pypi/django-treebeard),
is not compatible with Django-1.10 although pull requests have been made available by third parties.

Furthermore, the website containing the documentation of the project, is down quite often
and apparently he has no intention to host it on [Read the Docs](https://readthedocs.org/).

I also would prefer to run the unit test suite on a public service such as Travis-CI rather than
a private Jenkins installation.

This is a major problem for the Django community, since this implementation is used widely to
implement hierarchical trees in relational databases, for instance the pages and plugin models in
django-CMS. Relying on software whose documentation is not always available can be a major issue.


## Proposal to fork and rename django-treebeard

Therefore I'm proposing to fork this project, rename it and publish it on PyPI under a different
name.

The first version shall be compatible with the current version of django-treebeard. Later on we
might add additional functionality.


## Proposal for further improvement


### Use a Model Manager for object creation

Currently, method ``add_root`` is implemented as a class-method of the current model node. This is
not Djangonic behaviour. In Django, creating a new model instance, should be done through the model
manager and not through a class- or static method.


### Transform into a mixin class

Currently, models wanting materialized path-tree functionality, must inherit from
``treebeard.mp_tree.MP_Node``. This from an object oriented programming point of view is not the
best approach, because it is an enrichment of functionality rather than a IS-A relationship. I
therefore would propose to create a mixin class, which can be added to existing Django models.


### Allow to override field names

Currently, models inheriting from ``treebeard.mp_tree.MP_Node`` have three hard coded fields:
`` path``, ``depth`` and ``numchild``. Combining this with existing model definitions can lead to
naming conflicts. I therefore suggest to allow the inheriting class to override these three names.
The inheriting class is responsible for all database migrations anyway, so this should not cause
any problems.


### Multiple tree roots

Currently django-treebeard does not offer any functionality to handle more than one root. This has
to be implemented by the model inheriting from ``treebeard.mp_tree.MP_Node``. If one has the need
to implement this, an additional field has to be added in order to distinguish the different roots.
Currently, when adding a child to an existing node, the value of this field must be copied from the
parent node to it's child. In a future version of treebeard, this operation should be performed by
the existing ``add_child()`` method in a transparent manner.


### Enhance Administration backend

If one wants to add a child to an existing node using the administration backend, currently there
is no intuitive way. In my opinion, the change list view of a model shall use the jstree.js together
with the grid library. This would allow us to render all the fields which should be visible in
the list view. It also would allow us to add child nodes using a a special ``+`` button or a context
menu. It also would allow the administrator to move nodes through drag and drop.