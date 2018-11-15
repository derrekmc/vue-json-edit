<template>
	<div class="j-w">
		<h1 class="t">Vue-Json-Edit</h1>
		<div class="editor-w clearfix">
			<div class="w-2">
				<div class="editor">
					<JsonEditor :objData="jsonData" v-model="jsonData" ></JsonEditor>
				</div>
			</div>
			<div class="w-2">
				<div class="code-pre">
					<div slot="content">
						<pre>
							<code class="json" id="res_code"></code>
						</pre>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import hljs from 'highlight.js'

export default {
	name: 'app',
	data: function() {
		return {
			jsonData: {
				name: 'jinkin',
				age: 12,
				address: ['Panyu Shiqiao on Canton', 'Tianhe', {
					namll: 'world inside',
					city: 'forida meta 11'
				}, ['nammm', 'fefasas', 'cadasda'], {
					ge: 'asdasdasd',
					grqq: 'adsadasdsad'
				}],
				ohters: {
					id: 1246,
					joinTime: '2017-08-20. 10:20',
					description: 'another man'
				}
			}
		}
	},
	watch: {
		'jsonData': function () {
			let c = this.formatJson(JSON.stringify(this.jsonData))
			this.drawResCode(c)
		}
	},

	methods: {
	
		//JSON format print
		formatJson: function(txt, compress /*Whether it is compressed mode*/) {
			/* Format JSON source code (objects are converted to JSON text) */
			var indentChar = "  ";
			if (/^\s*$/.test(txt)) {
				console.error("The data is empty and cannot be formatted! ");
				return;
			}
			try {
				var data = eval("(" + txt + ")");
			} catch (e) {
				throw ("Data source syntax error, formatting failed! Error message: " + e.description, "err");
				return;
			}
			var draw = [],
				last = false,
				This = this,
				line = compress ? "" : "\n",
				nodeCount = 0,
				maxDepth = 0;

			var notify = function(name, value, isLast, indent /*indentation*/, formObj) {
				nodeCount++; /*Node Count*/
				for (var i = 0, tab = ""; i < indent; i++) tab += indentChar; /* indentationHTML */
				tab = compress ? "" : tab; /**/
				maxDepth = ++indent; /*Compressed mode ignores indentation*/
				if (value && value.constructor == Array) {
					/*Processing Array*/
					draw.push(
						tab + (formObj ? '"' + name + '":' : "") + "[" + line
					); /*indentation'[' Then wrap*/
					for (var i = 0; i < value.length; i++)
						notify(i, value[i], i == value.length - 1, indent, false);
					draw.push(
						tab + "]" + (isLast ? line : "," + line)
					); /*indentation']'Wrap, add a comma if it is not a tail element*/
				} else if (value && typeof value == "object") {
					/*处理对象*/
					draw.push(
						tab + (formObj ? '"' + name + '":' : "") + "{" + line
					); /*indentation'{' Then wrap*/
					var len = 0,
						i = 0;
					for (var key in value) len++;
					for (var key in value) notify(key, value[key], ++i == len, indent, true);
					draw.push(
						tab + "}" + (isLast ? line : "," + line)
					); /*indentation'}'Wrap, add a comma if it is not a tail element*/
				} else {
					if (typeof value == "string") value = '"' + value + '"';
					draw.push(
						tab +
						(formObj ? '"' + name + '":' : "") +
						value +
						(isLast ? "" : ",") +
						line
					);
				}
			};
			var isLast = true,
				indent = 0;
			notify("", data, isLast, indent, false);
			return draw.join("");
		},

		//Display res body
		drawResCode: function (content) {
			var target = document.getElementById('res_code');
			target.textContent = content
			hljs.highlightBlock(target)
		},
	},
	mounted: function() {
		let c = this.formatJson(JSON.stringify(this.jsonData))
		this.drawResCode(c)
	}
}
</script>

<style>
@import url('../node_modules/highlight.js/styles/github.css');


</style>


