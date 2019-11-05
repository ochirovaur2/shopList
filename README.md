<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Shopping list</title>
	<style type="text/css">
		*{
	margin: 0;
	padding: 0;
	background: #d6d6d6;
	box-sizing: border-box;
	}

	#main{
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		height: 620px;
		width: 310px;
		background: black;
		border-radius: 40px;
		border: 0.5px solid white;
		box-shadow:  0 0 0 3.5px #888;
		overflow: hidden;

	}
	#main::before{
		content: "";
		display: block;
		position: absolute;
		height: 800px;
		width: 320px;
		background:  rgb(255, 255, 255, 0.1);
		transform: translateX(60px) translateY(-20px) rotate(20deg);
		

	}

	#container{
		position: absolute;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		height: 520px;
		width: 285px;
		background: white;
	}

	#shopList{
		position: relative;
		margin-top: 0px;
		width: 100%;
		height: 330px;
		background: #f4f4f4;
		overflow: auto;
	}
	.clearfix::after {
	    content: "";
	    display: table;
	    clear: both;
	}
	li{
		list-style: none;
		text-align: center;
		line-height: 20px;
	}

	li:first-child { border-top: 1px solid #e7e7e7; }
	li:nth-child(even) { background-color: #f7f7f7; }
	li span{
		background: inherit;
	}
	#budget{
		width: 100%;
		height: 50px;
	}
	#budget h3{
		margin-left: 10px;
		line-height: 30px;
	}
	#budget h3 span{
		font-weight:  normal;
	}
	.deleteItem{
		background: none;
		border: none;
		float: right;
		margin-right: 5px;
		margin-top: 10px;
		transition: 0.5s;
	}
	.deleteItem:hover{
		cursor: pointer;
		color: red;
	}

	#inputDiv{
		
		width: 100%;
		height: 90px;
		background: #339AF0;
	}

	#inputText{
		
		border: none;
		width: 240px;
		height: 25px;
		margin: 10px 20px 5px;
		border-radius: 5px;
		background: #f4f4f4;
	}

	#inputValue{
		width: 170px;
		height: 25px;
		margin: 0px 20px;
		border: none;
		border-radius: 5px;
		background: #f4f4f4;

	}
	#inputBtn{
		margin-left: 20px;
		font-size: 25px;
		border: none;
		background: none;
		transition: 0.5s;
		color: white;

	}

	#inputText:focus,
	#inputValue:focus{
		border: 2px solid #00FF00;
	}


	#inputBtn:hover{
		color: #00FF00;
		cursor: pointer;
	}
	h1{
		height: 50px;
		line-height: 50px;
		text-align: center;
		background: white;
		background: rgb(51, 154, 240, 0.7);
	}
	</style>
</head>
<body>
	<div id="main">
		<div id="container">
			<h1>Shopping List</h1>
			<div id="shopList">
				
			</div>
			<div id="budget">
				<h3>Total: <span id="budgetVal">-</span></h3>

			</div>
			<div id="inputDiv">
				<input type="text" id="inputText" autofocus>
				<input type="number" id="inputValue">
				<button id="inputBtn">&#10004;</button>
				
			</div>
		</div>
	</div>
	<script >
		
// Data Controller
var dataController = (function(){
	

	var data = {
		items: [],
		budget: 0
	};

	var Item = function(des, val, id){
		this.des = des;
		this.val = val;
		this.id = id;
	}

	var addItemToTheData = function(newItem){
		data.items.push(newItem);
	};

	var calculateTotal = function(){

		var sum = 0;


		data.items.forEach(function(cur){
			
		

			sum += cur.val;

		});
		
		data.budget = sum;
		console.log(data.budget);
	};

	

	return {
		addItem: function(description, value){
			var newItem, ID;

			if(data.items.length > 0){
				ID = data.items[data.items.length - 1].id + 1;
			} else {
				ID = 0;
			}

			newItem = new Item(description, value, ID);

			console.log(newItem);
			
			addItemToTheData(newItem)

			console.log(data)

			return newItem;
		},
		calculateBudget: function(){
			calculateTotal();
		},
		getBudget: function(){
			return data.budget
		},
		deleteItemFromData: function (id){
		console.log(id);
		var IDs, index;
		IDs = data.items.map(function(current) {
            return current.id;
        });
		console.log(IDs);

        index = IDs.indexOf(+id);
        console.log(index)
        if(index !== -1){
        	data.items.splice(index, 1);
        }
        console.log(data.items)
	}
		
	}


})();


//UI Controller

var UIController = (function(){

	var DOMstrings = {
		inputText: 'inputText',
		inputValue: 'inputValue',
		inputBtn: 'inputBtn',
		container: 'shopList',
		budget: 'budgetVal',

	};

	var formatNumber = function(num){

		var numSplit, int, dec, type;
		num = Math.abs(num);
		
		num = num.toFixed(2);

		numSplit = num.split('.');
		int = numSplit[0]; 
		if (int.length > 3) {
            int = int.substr(0, int.length - 3) + ',' + int.substr(int.length - 3, 3); //input 23510, output 23,510
        }
        dec = numSplit[1]
		
		return int + "." + dec;
	};

	return {
		getDOMstrings: function(){
			return DOMstrings;
		},
		getInput: function(){
			return {
				text: document.getElementById(DOMstrings.inputText).value,
				value: parseInt(document.getElementById(DOMstrings.inputValue).value)
			}
		},
		displayItem: function(id, des, val){
			var html, newHtml, container;
			container = document.getElementById(DOMstrings.container);

			// Create HTML string with placeholder text

			html = `
				<li class="clearfix" id="%id%">%description% 
					<button class="deleteItem">&#10007;</button>
					<br>
					<span class="item__value">%value%</span>
					
				</li>
				`
			//Replace placeholder with new item's data

			newHtml = html.replace('%id%', id);
			newHtml = newHtml.replace('%description%', des);
			newHtml = newHtml.replace('%value%', formatNumber(val) );

			console.log(newHtml);

			//Insert item to the shop list
			container = document.getElementById(DOMstrings.container);
			container.insertAdjacentHTML('beforeend', newHtml);

		},
		clearFields: function(){
			document.getElementById(DOMstrings.inputText).value = "";
			document.getElementById(DOMstrings.inputValue).value = "";
			document.getElementById(DOMstrings.inputText).focus();
		},
		displayBudget: function(budget){
			var DOMForBudget;

			DOMForBudget = document.getElementById(DOMstrings.budget)
			if(budget >= 0) {
				DOMForBudget.textContent = formatNumber(budget);
			} else {
				DOMForBudget.textContent = "-";
			}
			
		},
		removeItemFromUI: function(itemID){

			document.getElementById(itemID).remove();
			
		}
	}

})();

//GLOBAL APP CONTROLLER

var controller = (function(dataCtrl, UICtrl){
	var DOM = UICtrl.getDOMstrings();
	var setupEventListeners = function(){
		
		

		document.getElementById(DOM.inputBtn).addEventListener('click', ctrlAddItem);

		document.addEventListener('keypress', function(ev){

			if(event.keyCode === 13 || event.which === 13){
				ctrlAddItem();
			}

		});

		document.getElementById(DOM.container).addEventListener('click', ctrlDeleteItem)


	}

	

	var ctrlAddItem = function(){
		var inputVal, inputTxt, values, newItem, budget;
		// Get input
		inputVal = document.getElementById(DOM.inputValue).value;
		inputTxt = document.getElementById(DOM.inputText).value;
		if(inputTxt && inputVal){
		values = UICtrl.getInput();

		// Add the item to the Data

		newItem = dataCtrl.addItem(values.text, values.value)
		

		dataCtrl.calculateBudget();

		// Add the item to the UI

		
		UICtrl.displayItem(newItem.id, newItem.des, newItem.val);
		

		// Clear the fields

		UICtrl.clearFields();

		// Calculate and update budget

		dataCtrl.calculateBudget();

		//Display budget on UI

		budget = dataCtrl.getBudget();

		UICtrl.displayBudget(budget);

		}
	}
	var ctrlDeleteItem = function(event){
		var budget, itemID

		itemID = event.target.parentNode.id;

		if(!isNaN(itemID)){

		console.log(itemID)

		// Delete item from data
		dataCtrl.deleteItemFromData(itemID);

		// Delete item from UI

		UICtrl.removeItemFromUI(itemID);
		
		// Calculate budget

		dataCtrl.calculateBudget();


		// Display recalculated budget

		budget = dataCtrl.getBudget();

		UICtrl.displayBudget(budget);
		}
	}
	return{
		initFunction: function(){
			console.log('Ready to work!');
			setupEventListeners();
		}
	}

	})(dataController, UIController);

	controller.initFunction();

	</script>
</body>
</html>
