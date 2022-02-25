# Getting Started to Pharo-AI

To install Pharo-AI, please execute the following code snipped in a Pharo Playground:

```st
EpMonitor disableDuring: [
    Metacello new
        baseline: 'AIPharo';
        repository: 'github://pharo-ai/ai/src';
        onWarningLog;
        load ]
```

If you want to see the status of all our currently available projects, please refer to: https://github.com/pharo-ai/ai

## How to contribute

This is an open-source community project so we are more than happy to receive contributions.

For developing proposes, we have the different projects/algorithms in several github repositories. In each of the wiki page we put a link of the specific repository in which that project is located.

If you want to fix an issue you can do a Pull Request. You can see more information on the [Contribute a fix to Pharo](https://github.com/pharo-project/pharo/wiki/Contribute-a-fix-to-Pharo) wiki page.

Finally, if you have some idea or project you want to discuss we are also welcome to hear your ideas. Please send an email to any of the [Pharo mailing lists](https://pharo.org/community)