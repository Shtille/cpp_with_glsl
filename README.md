# C++ with GLSL Syntax Package for Sublime Text

There is no ready-made plugin on Package Control specifically built to highlight GLSL inside C++ string literals. However, you can achieve this easily in Sublime Text by combining a standard GLSL syntax package with a small custom syntax override for C++ raw string literals.

## Install

### Step 1: Install a GLSL Syntax Package

Before Sublime Text can highlight GLSL inside another language, it needs a GLSL syntax definition installed.

• **Install OpenGL Shading Language (GLSL).** Open the Command Palette (`Ctrl+Shift+P` or `Cmd+Shift+P`), select **Package Control: Install Package**, and install [OpenGL Shading Language (GLSL)](https://packagecontrol.io/packages/OpenGL%20Shading%20Language%20%28GLSL%29).
• **Verify the scope name.** This package registers the `source.glsl` scope, which you will embed into your C++ files.

### Step 2: Create a Custom C++ Syntax Extension

Sublime Text's `.sublime-syntax` engine natively supports embedding one language inside another using `embed` and `escape` rules. You can target C++11 raw string literals with a specific delimiter (such as `R"glsl(...)glsl"`) so normal C++ strings are unaffected.

• **Create a new syntax file**. In Sublime Text, go to **Tools > Developer > New Syntax...** and save the file in your `Packages/User/` folder as `C++_GLSL.sublime-syntax`.
• **Add the embedding rules.** Replace the template contents with the following YAML configuration, which extends the default C++ syntax and embeds GLSL inside `R"glsl(...)glsl"` blocks:
```yaml
%YAML 1.2
---
name: C++ (with GLSL)
scope: source.c++.glsl
file_extensions: [cpp, hpp, cc, h]
extends: Packages/C++/C++.sublime-syntax

contexts:
  strings:
	- meta_prepend: true
	- match: 'R"(glsl|GLSL)\('
	  scope: punctuation.definition.string.begin.c++
	  embed: scope:source.glsl
	  embed_scope: meta.embedded.block.glsl
	  escape: '\)\1"'
	  escape_captures:
		0: punctuation.definition.string.end.c++
```
• **Select the new syntax.** Open any `.cpp` file, click the syntax name in the bottom-right corner of the status bar, and select **Open all with current extension as... > C++ (with GLSL).**

### Step 3: Style the Background in Your Color Scheme

Next, tell Sublime Text what background color to render for `meta.embedded.block.glsl`:

• **Open your color scheme customization.** Open the Command Palette (`Ctrl+Shift+P` or `Cmd+Shift+P`) and select **UI: Customize Color Scheme**.
• **Add the background rule.** A split window will open. On the right-hand side (`User` overrides), add the following rule inside the `"rules"` array:
```json
{
	"rules":
	[
		{
			"name": "Embedded GLSL Block Background",
			"scope": "meta.embedded.block.glsl",
			"background": "#38414B"
		}
	]
}
```
• **Adjust the hex color (`#38414B`).** Pick a shade that is slightly darker or slightly lighter than your current editor background so the GLSL block stands out as a subtle tinted box without reducing text contrast.

## Usage Example

Once configured, any shader written using the `glsl` raw string delimiter will automatically receive full GLSL syntax highlighting while remaining valid C++11 code:

```cpp
const char* vertexShader = R"glsl(
	#version 330 core
	layout (location = 0) in vec3 aPos;
	void main() {
		gl_Position = vec4(aPos, 1.0);
	}
)glsl";
```