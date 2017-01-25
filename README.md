# moore3071.github.io

[![Build Status](https://travis-ci.org/moore3071/moore3071.github.io.svg?branch=master)](https://travis-ci.org/moore3071/moore3071.github.io)

This is my personal website built with Jekyll. It was an training ground for me to practice CSS Flexboxes (although hopefully I'll be able to use CSS Grids to improve the appearance of technologies in the about page once more browsers adopt CSS Grids). As it is licensed under the MIT license, feel free to use this code as a basis for your own project.

## Dependencies/Development

In order to set up this environment, you will need Ruby installed along with the Gem bundler. From there you simply need to run
```bash
bundle install
```
From there you can start developing while referring to [Jekyll's sufficient documentation](https://jekyllrb.com/docs/usage/). Don't forget about bundler though as mentioned in [Jekyll's quickstart page](https://jekyllrb.com/docs/quickstart/).

Additionally you can check that links and images all exist using the script script/ci-check. You'll need to make this executable first with
```bash
chmod +x script/ci-check
```
