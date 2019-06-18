
```
exc.ui.components.definitions["textbox"] = {
	uiw: 'textbox',
	selectors: ["[data-uiw='textbox']"],
	vs: "value", //value source

	methods : { //additional functionality added to the element
		play: function(){
			console.log(this.uiw);
			console.log("at play...");
		}
	},
	//init: function(elm){ }

	build: function(elm){
		return obj;
	}
	renderCompleted: function(o){


	}

}
```
