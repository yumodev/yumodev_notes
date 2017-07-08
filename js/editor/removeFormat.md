

var selection = frames["richedit"].getSelection();
var selectedText = selection.toString();
var range = selection.getRangeAt(0);
var span = frames["richedit"].document.createElement("span");
span.style.backgroundColor = "yellow";
range.surroundContents(span);

removeFormatP{

	var selection = document.getSelection();
	var range = selection.getRangeAt(0);
	var span = document.createElement("span");
	range.surroundContents(span);
}





