<!-- TITLE: Jest -->
<!-- SUBTITLE: Adding Jest to a node project -->

# Packages
```text
npm install --save-dev jest jest babel-core babel-preset-env babel-jest
```

Note, `babel-jest` is required for using ES6 syntax within the tests.
# .babelrc
```text
{
  "presets": ["env"]
}
```

# package.json
Before the devDependencies, insert:
```text
"jest": {
    "transform": {
      ".*": "babel-jest"
    }
  },
  ```
	
	# Test Files
	Test filenames to have the convention: `<name>.test.js`. By default, jest will do a full subdirectory search for any such file.
	