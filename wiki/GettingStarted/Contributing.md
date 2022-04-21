# pharo-ai contribution guide

First of all, you can see the [Pharo contribution guide](https://github.com/pharo-project/pharo/blob/Pharo11/CONTRIBUTING.md). There are explained all the principla things for contributing with a Pull Request, opening an issue, and so on.

## Proposing new features or algorithms

If you have some idea you are welcome to propose it. If it is an idea for an existing algorithm, you can create an issue in the correspondig repository. Feel free to tag the mainteiners.

If you would like to implement a new algoritm, you can also create an issue, or you can send an email to lse-pharoai@inria.fr

## Injecting external dependencies

For us, an external dependency is dependency that lives outside pharo-ai.

If you are implementing a new algorithm or if you are working on an existing one, and if you need to add an external dependencies, please do it using [external-dependencies](https://github.com/pharo-ai/external-dependencies) package. There is a README file where it is explained how to use it.

But basically, it is a package that only containts a baseline that will load for you an external dependency, like [PolyMath](https://github.com/PolyMathOrg/PolyMath) or [Roassal3](https://github.com/ObjectProfile/Roassal3). It manages for you the version of the dependency. Like that, if in the future we want to change the version of PolyMath that we are using, we only need to do it in one place.