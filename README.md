# EZPZ - Starter SFCs for Vue.JS #
EZPZ is meant to be a "starter framework" set of SFCs for quick website / app development on Vue.JS. You can clone the repository and make changes as necessary for your project. If you have global changes that you think would benefit the repo, or have added new components, feel free to submit a pull request. May package this up at some point with a builder like Rollup, but in the meantime, just clone it down and get to work!

## ABOUT SCSS VARIABLES ##
There are a number of variables used by these components to assign basic colors. When using these components in your project, you're going to want to make sure you define those variables anywhere you use these components. Most of the time, this is done using your chosen app packager, for example using the sass-loader package in webpack, and having a separate variables.scss file that gets loaded in. Below is a basic set of the variables used by this plugin. Feel free to copy the basic set as is or modify as needed. Just make sure you include all of them somewhere!

	$main-black: #000000;
	
	$light-bg-color: #ffffff;
	$mid-bg-color: #eaeaea;
	$dark-bg-color: #000000;
	
	$light-text-color:#ffffff;
	$dark-text-color:#000000;
	
	$form-success: #a4b854;
	$form-error: #A22522;
	$form-warn: #FAA613;
	$form-info: #56A8FF;
