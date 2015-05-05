/
Copyright (c) 2011 The Wojo Group

thewojogroup.com
simplecartjs.com
http://github.com/thewojogroup/simplecart-js/tree/master

The MIT License

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
/

var Custom="Custom",GoogleCheckout="GoogleCheckout",PayPal="PayPal",Email="Email",IndonesianRupiah="IDR",IDR="IDR",AustralianDollar="AUD",AUD="AUD",CanadianDollar="CAD",CAD="CAD",CzechKoruna="CZK",CZK="CZK",DanishKrone="DKK",DKK="DKK",Euro="EUR",EUR="EUR",HongKongDollar="HKD",HKD="HKD",HungarianForint="HUF",HUF="HUF",IsraeliNewSheqel="ILS",ILS="ILS",JapaneseYen="JPY",JPY="JPY",MexicanPeso="MXN",MXN="MXN",NorwegianKrone="NOK",NOK="NOK",NewZealandDollar="NZD",NZD="NZD",PolishZloty="PLN",PLN="PLN",PoundSterling="GBP",GBP="GBP",SingaporeDollar="SGD",SGD="SGD",SwedishKrona="SEK",SEK="SEK",SwissFranc="CHF",CHF="CHF",ThaiBaht="THB",THB="THB",USDollar="USD",USD="USD";
function Cart(){

> var me = this;
> /**member variables**/
> me.nextId = 1;
> me.Version = '2.2.2';
> me.Shelf = null;
> me.items = {};
> me.isLoaded = false;
> me.pageIsReady = false;
> me.quantity = 0;
> me.total = 0;
> me.taxRate = 0;
> me.taxCost = 0;
> me.shippingFlatRate = 0;
> me.shippingTotalRate = 0;
> me.shippingQuantityRate = 0;
> me.shippingRate = 0;
> me.shippingCost = 0;
> me.currency = TRY;
> me.checkoutTo = PayPal;
> me.email = "";
> me.merchantId	 = "";
> me.successUrl = null;
> me.cancelUrl = null;
> me.cookieDuration = 30; // default duration in days
> me.storagePrefix = "sc**";
> me.MAX\_COOKIE\_SIZE = 4000;
> me.cartHeaders = ['Name','Price','Quantity','Total'];
> me.events = {};
> me.sandbox = false;
> me.paypalHTTPMethod = "GET";
> /_> > cart headers:
> > you can set these to which ever order you would like, and the cart will display the appropriate headers
> > and item info.	any field you have for the items in the cart can be used, and 'Total' will automatically
> > be price\*quantity._


> there are keywords that can be used:**

  1. "_input" - the field will be a text input with the value set to the given field. when the user
> > changes the value, it will update the cart.	 this can be useful for quantity. (ie "Quantity\_input")_


> 2) "increment" - a link with "+" that will increase the item quantity by 1

> 3) "decrement" - a link with "-" that will decrease the item quantity by 1

> 4) "remove" - a link that will remove the item from the cart

> 5) "_image" or "Image" - the field will be an img tag with the src set to the value. You can simply use "Image" if
> > you set a field in the items called "Image".  If you have a field named something else, like "Thumb", you can add
> > the "_image" to create the image tag (ie "Thumb\_image").


> 6) "_noHeader" - this will skip the header for that field (ie "increment\_noHeader")_


  * 




> /
> > add/remove items to cart
    * /


> me.add = function ( values ) {
> > var me=this;
> > /**load cart values if not already loaded**/
> > if( !me.pageIsReady		) {
> > > me.initializeView();
> > > me.update();

> > }
> > if( !me.isLoaded		) {
> > > me.load();
> > > me.update();

> > }


> var newItem = new CartItem();

> /**check to ensure arguments have been passed in**/
> if( !arguments || arguments.length === 0 ){
> > error( 'No values passed for item.');
> > return null;

> }
> var argumentArray = arguments;
> if( values && typeof( values ) !== 'string' && typeof( values ) !== 'number'  ){
> > argumentArray = values;

> }

> newItem.parseValuesFromArray( argumentArray );
> newItem.checkQuantityAndPrice();

> if( me.trigger('beforeAdd', [newItem](newItem.md) ) === false ){
> > return false;

> }
> var isNew = true;

> /**if the item already exists, update the quantity**/
> if( me.hasItem(newItem) ) {
> > var foundItem=me.hasItem(newItem);
> > foundItem.quantity= parseInt(foundItem.quantity,10) + parseInt(newItem.quantity,10);
> > newItem = foundItem;
> > isNew = false;

> } else {
> > me.items[newItem.id] = newItem;

> }

> me.update();
> me.trigger('afterAdd', [newItem,isNew] );

> return newItem;

> };


> me.remove = function( id ){
> > var tempArray = {};


> me.each(function(item){
> > if( item.id !== id ){
> > > tempArray[item.id] = item;

> > }

> });
> this.items = tempArray;
> };

> me.empty = function () {
> > me.items = {};
> > me.update();

> };

> /
> > item accessor functions
    * /


> me.find = function (criteria) {
> > if( !criteria ){
> > > return null;

> > }


> var results = [.md](.md);

> me.each(function(item,x,next){

> fits = true;

> me.each( criteria , function(value,j,name){
> > if( !item[name](name.md) || item[name](name.md) != value ){
> > > fits = false;

> > }

> });

> if( fits ){
> > results.push( item );

> }
> });
> return (results.length === 0 ) ? null : results;
> };


> me.each = function( array , callback ){
> > var next,
> > > x=0,
> > > result;


> if( typeof array === 'function' ){
> > var cb = array
> > > items = me.items;

> } else if( typeof callback === 'function' ){
> > var cb = callback,
> > > items = array;

> } else {
> > return;

> }

> for( next in items ){
> > if( typeof items[next](next.md) !== "function" ){
> > > result = cb.call( me , items[next](next.md) , x , next );
> > > if( result === false ){
> > > > return;

> > > }
> > > x++;

> > }

> }

> };


> me.chunk = function(str, n) {
> > if (typeof n==='undefined'){
> > > n=2;

> > }
> > var result = str.match(RegExp('.{1,'+n+'}','g'));
> > return result || [.md](.md);

> };


> /
> > checkout management
    * /


> me.checkout = function() {
> > if( me.quantity === 0 ){
> > > error("Cart is empty");
> > > return false;

> > }
> > switch( me.checkoutTo ){
> > > case PayPal:
> > > > me.paypalCheckout();
> > > > break;

> > > case GoogleCheckout:
> > > > me.googleCheckout();
> > > > break;

> > > case Email:
> > > > me.emailCheckout();
> > > > break;

> > > default:
> > > > me.customCheckout();
> > > > break;

> > }

> };

> me.paypalCheckout = function() {


> var form = document.createElement("form"),
> > counter=1,
> > current,
> > item,
> > descriptionString;


> form.style.display = "none";
> form.method = me.paypalHTTPMethod =="GET" || me.paypalHTTPMethod == "POST" ? me.paypalHTTPMethod : "GET";
> form.action = me.sandbox ? "https://www.sandbox.paypal.com/cgi-bin/webscr" : "https://www.paypal.com/cgi-bin/webscr";
> form.acceptCharset = "utf-8";


> // setup hidden fields
> form.appendChild(me.createHiddenElement("cmd", "_cart"));
> form.appendChild(me.createHiddenElement("rm", me.paypalHTTPMethod == "POST" ? "2" : "0" ));
> form.appendChild(me.createHiddenElement("upload", "1"));
> form.appendChild(me.createHiddenElement("business", me.email ));
> form.appendChild(me.createHiddenElement("currency\_code", "me.currency"));_

> if( me.taxRate ){
> > form.appendChild(me.createHiddenElement("tax\_cart",me.taxCost ));

> }

> if( me.shipping() !== 0){
> > form.appendChild(me.createHiddenElement("handling\_cart",  me.shippingCost ));

> }

> if( me.successUrl ){
> > form.appendChild(me.createHiddenElement("return",  me.successUrl ));

> }

> if( me.cancelUrl ){
> > form.appendChild(me.createHiddenElement("cancel\_return",  me.cancelUrl ));

> }



> me.each(function(item,iter){

> counter = iter+1;

> form.appendChild( me.createHiddenElement( "item\_name_"		+ counter, item.name		) );
> form.appendChild( me.createHiddenElement( "quantity_"		+ counter, item.quantity	) );
> form.appendChild( me.createHiddenElement( "amount_"			+ counter, item.price		) );
> form.appendChild( me.createHiddenElement( "item\_number_"	+ counter, counter			) );

> var option\_count = 0;

> me.each( item , function( value, x , field ){
> > if( field !== "id" && field !== "price" && field !== "quantity" && field !== "name" && field !== "shipping" && option\_count < 10) {
> > > form.appendChild( me.createHiddenElement( "on" + option\_count + "_"	+ counter, 	field ) );
> > > form.appendChild( me.createHiddenElement( "os" + option\_count + "_"	+ counter, 	value ) );
> > > option\_count++;

> > }

> });

> form.appendChild( me.createHiddenElement( "option\_index_" + counter, option\_count) );_

> });


> document.body.appendChild( form );
> form.submit();
> document.body.removeChild( form );

> };

> me.googleCheckout = function() {
> > var me = this;



> if( me.currency !== TRY && me.currency !== GBP ){
> > error( "Google Checkout only allows the USD and GBP for currency.");
> > return;

> } else if( me.merchantId === "" | me.merchantId === null || !me.merchantId ){|
|:-----------------------|
> > error( "No merchant Id for google checkout supplied.");
> > return;

> }

> var form = document.createElement("form"),
> > counter=1,
> > current,
> > item,
> > descriptionString;


> form.style.display = "none";
> form.method = "POST";
> form.action = "https://checkout.google.com/api/checkout/v2/checkoutForm/Merchant/" +
> > me.merchantId;

> form.acceptCharset = "utf-8";

> me.each(function(item,iter){

> counter = iter+1;

> form.appendChild( me.createHiddenElement( "item\_name_"		+ counter, item.name		) );
> form.appendChild( me.createHiddenElement( "item\_quantity_"	+ counter, item.quantity	) );
> form.appendChild( me.createHiddenElement( "item\_price_"		+ counter, item.price		) );
> form.appendChild( me.createHiddenElement( "item\_currency_"	+ counter, me.currency		) );
> form.appendChild( me.createHiddenElement( "item\_tax\_rate_"	+ counter, me.taxRate		) );
> form.appendChild( me.createHiddenElement( "_charset_"				 , ""				) );_

> descriptionString = "";

> me.each( item , function( value , x , field ){

> if( field !== "id"		&&
> > field !== "quantity" &&
> > field !== "price" ) {


> descriptionString = descriptionString + ", " + field + ": " + value;
> }
> });

> descriptionString = descriptionString.substring( 1 );
> form.appendChild( me.createHiddenElement( "item\_description_" + counter, descriptionString) );_

> });

> // hack for adding shipping
> if( me.shipping() !== 0){
> > form.appendChild(me.createHiddenElement("ship\_method\_name\_1", "Shipping"));
> > form.appendChild(me.createHiddenElement("ship\_method\_price\_1", parseFloat(me.shippingCost).toFixed(2)));
> > form.appendChild(me.createHiddenElement("ship\_method\_currency\_1", me.currency));

> }

> document.body.appendChild( form );
> form.submit();
> document.body.removeChild( form );
> };



> me.emailCheckout = function() {
> > return;

> };

> me.customCheckout = function() {
> > return;

> };




> /
> > data storage and retrival
    * /


> /**load cart from cookie**/
> me.load = function () {
> > var me = this,
> > > id;


> /**initialize variables and items array**/
> me.items = {};
> me.total = 0.00;
> me.quantity = 0;

> /**retrieve item data from cookie**/
> if( readCookie(simpleCart.storagePrefix + 'simpleCart_' + "chunks") ){
> > var chunkCount = 1\*readCookie(simpleCart.storagePrefix + 'simpleCart_' + "chunks"),
> > > dataArray = [.md](.md),
> > > dataString = "",
> > > data = "",
> > > info,
> > > newItem,
> > > y=0;

> > if(chunkCount>0) {
> > > for( y=0;y<chunkCount;y++){
> > > > dataArray.push( readCookie( simpleCart.storagePrefix + 'simpleCart_' + (1 + y ) ) );

> > > }_


> dataString = unescape( dataArray.join("") );
> data = dataString.split("++");
> }
> for(var x=0, xlen=data.length;x<xlen;x++){

> info = data[x](x.md).split('||');
> newItem = new CartItem();

> if( newItem.parseValuesFromArray( info ) ){
> > newItem.checkQuantityAndPrice();
> > /**store the new item in the cart**/
> > me.items[newItem.id] = newItem;

> }
> }
> }

> me.isLoaded = true;
> };



> /**save cart to cookie**/
> me.save = function () {
> > var dataString = "",
> > > dataArray = [.md](.md),
> > > chunkCount = 0;


> chunkCount = 1\*readCookie(simpleCart.storagePrefix + 'simpleCart_' + "chunks");
> for( var j=0;j<chunkCount;j++){
> > eraseCookie(simpleCart.storagePrefix + 'simpleCart_'+ j);

> }
> eraseCookie(simpleCart.storagePrefix + 'simpleCart_' + "chunks");_


> me.each(function(item){
> > dataString = dataString + "++" + item.print();

> });

> dataArray = simpleCart.chunk( dataString.substring(2) , simpleCart.MAX\_COOKIE\_SIZE );

> for( var x=0,xlen = dataArray.length;x<xlen;x++){
> > createCookie(simpleCart.storagePrefix + 'simpleCart_' + (1 + x ), dataArray[x](x.md), me.cookieDuration );

> }_

> createCookie( simpleCart.storagePrefix + 'simpleCart_' + "chunks", "" + dataArray.length , me.cookieDuration );
> };_



> /
> > view management
    * /


> me.initializeView = function() {
> > var me = this;
> > me.totalOutlets				= getElementsByClassName('simpleCart\_total');
> > me.quantityOutlets			= getElementsByClassName('simpleCart\_quantity');
> > me.cartDivs					= getElementsByClassName('simpleCart\_items');
> > me.taxCostOutlets			= getElementsByClassName('simpleCart\_taxCost');
> > me.taxRateOutlets			= getElementsByClassName('simpleCart\_taxRate');
> > me.shippingCostOutlets		= getElementsByClassName('simpleCart\_shippingCost');
> > me.finalTotalOutlets		= getElementsByClassName('simpleCart\_finalTotal');


> me.addEventToArray( getElementsByClassName('simpleCart\_checkout') , simpleCart.checkout , "click");
> me.addEventToArray( getElementsByClassName('simpleCart\_empty')	, simpleCart.empty , "click" );

> me.Shelf = new Shelf();
> me.Shelf.readPage();

> me.pageIsReady = true;

> };



> me.updateView = function() {
> > me.updateViewTotals();
> > if( me.cartDivs && me.cartDivs.length > 0 ){
> > > me.updateCartView();

> > }

> };

> me.updateViewTotals = function() {
> > var outlets = [["quantity"		, "none"		](.md) ,
> > > ["total"		, "currency"	] ,
> > > ["shippingCost" , "currency"	] ,
> > > ["taxCost"		, "currency"	] ,
> > > ["taxRate"		, "percentage"	] ,
> > > ["finalTotal"	, "currency"	] ];


> for( var x=0,xlen=outlets.length; x<xlen;x++){

> var arrayName = outlets[x](x.md)[0](0.md) + "Outlets",
> > outputString,
> > element;


> for( var y = 0,ylen = me[arrayName ](.md).length; y<ylen; y++ ){
> > switch( outlets[x](x.md)[1](1.md) ){
> > > case "none":
> > > > outputString = "" + me[outlets[x](x.md)[0](0.md)];
> > > > break;

> > > case "currency":
> > > > outputString = me.valueToCurrencyString( me[outlets[x](x.md)[0](0.md)] );
> > > > break;

> > > case "percentage":
> > > > outputString = me.valueToPercentageString( me[outlets[x](x.md)[0](0.md)] );
> > > > break;

> > > default:
> > > > outputString = "" + me[outlets[x](x.md)[0](0.md)];
> > > > break;

> > }
> > me[arrayName](arrayName.md)[y](y.md).innerHTML = "" + outputString;

> }
> }
> };

> me.updateCartView = function() {
> > var newRows = [.md](.md),
> > > y,newRow,current,header,newCell,info,outputValue,option,headerInfo;


> /**create headers row**/
> newRow = document.createElement('div');
> for(var y=0,ylen = me.cartHeaders.length; y<ylen; y++ ){
> > newCell = document.createElement('div');
> > headerInfo = me.cartHeaders[y](y.md).split("_");_


> newCell.innerHTML = me.print( headerInfo[0](0.md) );
> newCell.className = "item" + headerInfo[0](0.md);
> for(var z=1,zlen=headerInfo.length;z<zlen;z++){
> > if( headerInfo[z](z.md).toLowerCase() == "noheader" ){
> > > newCell.style.display = "none";

> > }

> }
> newRow.appendChild( newCell );

> }
> newRow.className = "cartHeaders";
> newRows[0](0.md) = newRow;

> /**create a row for each item in the cart**/
> me.each(function(item, x){
> > newRow = document.createElement('div');


> for(var y=0,ylen = me.cartHeaders.length; y<ylen; y++ ){
> > newCell = document.createElement('div');
> > info = me.cartHeaders[y](y.md).split("_");_


> outputValue = me.createCartRow( info , item , outputValue );

> newCell.innerHTML = outputValue;
> newCell.className = "item" + info[0](0.md);

> newRow.appendChild( newCell );
> }
> newRow.className = "itemContainer";
> newRows[x+1] = newRow;
> });



> for( var x=0,xlen=me.cartDivs.length; x<xlen; x++){

> /**delete current rows in div**/
> var div = me.cartDivs[x](x.md);
> if( div.childNodes && div.appendChild ){
> > while( div.childNodes[0](0.md) ){
> > > div.removeChild( div.childNodes[0](0.md) );

> > }



> for(var j=0, jLen = newRows.length; j<jLen; j++){
> > div.appendChild( newRows[j](j.md) );

> }
> }

> }
> };

> me.createCartRow = function( info , item , outputValue ){

> switch( info[0](0.md).toLowerCase() ){
> > case "total":
> > > outputValue = me.valueToCurrencyString(parseFloat(item.price)**parseInt(item.quantity,10) );
> > > break;

> > case "increment":
> > > outputValue = me.valueToLink( "+" , "javascript:;" , "onclick=\"simpleCart.items[\'" + item.id + "\'].increment();\"" );
> > > break;

> > case "decrement":
> > > outputValue = me.valueToLink( "-" , "javascript:;" , "onclick=\"simpleCart.items[\'" + item.id + "\'].decrement();\"" );
> > > break;

> > case "remove":
> > > outputValue = me.valueToLink( "Remove" , "javascript:;" , "onclick=\"simpleCart.items[\'" + item.id + "\'].remove();\"" );
> > > break;

> > case "price":
> > > outputValue = me.valueToCurrencyString( item[info[0](.md).toLowerCase() ] ? item[info[0](0.md).toLowerCase()] : " " );
> > > break;

> > default:
> > > outputValue = item[info[0](.md).toLowerCase() ] ?
> > > > typeof 	item[info[0](0.md).toLowerCase()] === 'function' ?
> > > > > item[info[0](0.md).toLowerCase()].call(item) :
> > > > > item[info[0](0.md).toLowerCase()] :
> > > > > " ";

> > > break;

> }**

> for( var y=1,ylen=info.length;y<ylen;y++){
> > option = info[y](y.md).toLowerCase();
> > switch( option ){
> > > case "image":
> > > case "img":
> > > > outputValue = me.valueToImageString( outputValue );
> > > > break;

> > > case "input":
> > > > outputValue = me.valueToTextInput( outputValue , "onchange=\"simpleCart.items[\'" + item.id + "\'].set(\'" + info[0](0.md).toLowerCase() + "\' , this.value);\""	);
> > > > break;

> > > case "div":
> > > case "span":
> > > case "h1":
> > > case "h2":
> > > case "h3":
> > > case "h4":
> > > case "p":
> > > > outputValue = me.valueToElement( option , outputValue , "" );
> > > > break;

> > > case "noheader":
> > > > break;

> > > default:
> > > > error( "unkown header option: " + option );
> > > > break;

> > }


> }
> return outputValue;
> };

> me.addEventToArray = function ( array , functionCall , theEvent ) {
> > var outlet,
> > > element;


> for(var x=0,xlen=array.length; x<xlen; x++ ){
> > element = array[x](x.md);
> > if( element.addEventListener ) {
> > > element.addEventListener(theEvent, functionCall , false );

> > } else if( element.attachEvent ) {
> > > element.attachEvent( "on" + theEvent, functionCall );

> > }

> }
> };


> me.createHiddenElement = function ( name , value ){
> > var element = document.createElement("input");
> > element.type = "hidden";
> > element.name = name;
> > element.value = value;
> > return element;

> };


> /
> > Event Management
    * /


> // bind a callback to a simpleCart event
> me.bind = function( name , callback ){
> > if( typeof callback !== 'function' ){
> > > return me;

> > }



> if (me.events[name](name.md) === true ){
> > callback.apply( me );

> } else if( typeof me.events[name](name.md) !== 'undefined' ){
> > me.events[name](name.md).push( callback );

> } else {
> > me.events[name](name.md) = [callback ](.md);

> }
> return me;
> };


> // trigger event
> me.trigger = function( name , options ){
> > var returnval = true;
> > if( typeof me.events[name](name.md) !== 'undefined' && typeof me.events[name](name.md)[0](0.md) === 'function'){
> > > for( var x=0,xlen=me.events[name](name.md).length; x<xlen; x++ ){
> > > > returnval = me.events[name](name.md)[x](x.md).apply( me , (options ? options : [.md](.md) ) );

> > > }

> > }
> > if( returnval === false ){
> > > return false;

> > } else {
> > > return true;

> > }

> };

> // shortcut for ready function
> me.ready = function( callback ){
> > if( !callback ){
> > > me.trigger( 'ready' );
> > > me.events['ready'] = true;

> > } else {
> > > me.bind( 'ready' , callback );

> > }
> > return me;

> };




> /
> > Currency management
    * /


> me.currencySymbol = function() {
> > switch(me.currency){
> > > case IDR:
> > > > return "Rp&nbsp;";

> > > case CHF:
> > > > return "CHF&nbsp;";

> > > case CZK:
> > > > return "CZK&nbsp;";

> > > case DKK:
> > > > return "DKK&nbsp;";

> > > case HUF:
> > > > return "HUF&nbsp;";

> > > case NOK:
> > > > return "NOK&nbsp;";

> > > case PLN:
> > > > return "PLN&nbsp;";

> > > case SEK:
> > > > return "SEK&nbsp;";

> > > case JPY:
> > > > return "&yen;";

> > > case EUR:
> > > > return "&euro;";

> > > case GBP:
> > > > return "&pound;";

> > > case CHF:
> > > > return "CHF&nbsp;";

> > > case THB:
> > > > return "&#3647;";

> > > case TRY:
> > > case CAD:
> > > case AUD:
> > > case NZD:
> > > case HKD:
> > > case SGD:
> > > > return "TL";

> > > default:
> > > > return "";

> > }

> };


> me.currencyStringForPaypalCheckout = function( value ){
> > if( me.currencySymbol() == "TL" ){
> > > return "$" + parseFloat( value ).toFixed(2);

> > } else {
> > > return "" + parseFloat(value ).toFixed(2);

> > }

> };

> /
> > Formatting
    * /



> me.valueToCurrencyString = function( value ) {
> > var val =  parseFloat( value );
> > if( isNaN(val))
> > > val = 0;


> return val.toCurrency( me.currencySymbol() );
> };

> me.valueToPercentageString = function( value ){
> > return parseFloat( 100\*value ) + "%";

> };

> me.valueToImageString = function( value ){
> > if( value.match(/<\s\*img.**src\=/) ){
> > > return value;

> > } else {
> > > return "<img src=\"" + value + "\" />";

> > }

> };**

> me.valueToTextInput = function( value , html ){
> > return "<input type=\"text\" value=\"" + value + "\" " + html + " />";

> };

> me.valueToLink = function( value, link, html){
> > return "<a href=\"" + link + "\" " + html + " >" + value + "

Unknown end tag for &lt;/a&gt;

";

> };

> me.valueToElement = function( type , value , html ){
> > return "<" + type + " " + html + " > " + value + "</" + type + ">";

> };

> /
> > Duplicate management
    * /


> me.hasItem = function ( item ) {
> > var current,
> > > matches,
> > > field,
> > > match=false;


> me.each(function(testItem){

> matches = true;

> me.each( item , function( value , x , field ){

> if( field !== "quantity" && field !== "id" && item[field](field.md) !== testItem[field](field.md) ){
> > matches = false;

> }
> });

> if( matches ){
> > match = testItem;

> }

> });
> return match;
> };

> /
> > Language managment
    * /

> me.ln = {
> > "en\_us": {
> > > quantity: "Quantity"
> > > , price: "Price"
> > > , total: "Total"
> > > , decrement: "Decrement"
> > > , increment: "Increment"
> > > , remove: "Remove"
> > > , tax: "Tax"
> > > , shipping: "Shipping"
> > > , image: "Image"

> > }

> };

> me.language = "en\_us";

> me.print = function( input ) {
> > var me = this;
> > return me.ln[me.language] && me.ln[me.language][input.toLowerCase()] ? me.ln[me.language][input.toLowerCase()] : input;


> };


> /
> > Cart Update managment
    * /


> me.update = function() {
> > if( !simpleCart.isLoaded ){
> > > simpleCart.load();

> > }
> > if( !simpleCart.pageIsReady ){
> > > simpleCart.initializeView();

> > }
> > me.updateTotals();
> > me.updateView();
> > me.save();

> };

> me.updateTotals = function() {

> me.total = 0 ;
> me.quantity	 = 0;
> me.each(function(item){

> if( item.quantity < 1 ){
> > item.remove();

> } else if( item.quantity !== null && item.quantity !== "undefined" ){
> > me.quantity = parseInt(me.quantity,10) + parseInt(item.quantity,10);

> }
> if( item.price ){
> > me.total = parseFloat(me.total) + parseInt(item.quantity,10)**parseFloat(item.price);

> }**

> });
> me.shippingCost = me.shipping();
> me.taxCost = parseFloat(me.total)**me.taxRate;
> me.finalTotal = me.shippingCost + me.taxCost + me.total;
> };**

> me.shipping = function(){
> > if( parseInt(me.quantity,10)===0 )
> > > return 0;

> > var shipping =	parseFloat(me.shippingFlatRate) +
> > > parseFloat(me.shippingTotalRate)**parseFloat(me.total) +
> > > parseFloat(me.shippingQuantityRate)**parseInt(me.quantity,10),
> > > next;


> me.each(function(nextItem){
> > if( nextItem.shipping ){
> > > if( typeof nextItem.shipping == 'function' ){
> > > > shipping += parseFloat(nextItem.shipping());

> > > } else {
> > > > shipping += parseFloat(nextItem.shipping);

> > > }

> > }

> });

> return shipping;
> }

> me.initialize = function() {
> > me.initializeView();
> > me.load();
> > me.update();
> > me.ready();

> };

}

/
  * Cart Item Object
  * /

function CartItem() {
> while( simpleCart.items["c" + simpleCart.nextId] )
> > simpleCart.nextId++;


> this.id = "c" + simpleCart.nextId;
}


CartItem.prototype = {

> set : function ( field , value ){
> > field = field.toLowerCase();
> > if( typeof( this[field](field.md) ) !== "function" && field !== "id" ){
> > > value = "" + value;
> > > if( field == "quantity"){
> > > > value = value.replace( /[^(\d|\.)]**/gi , "" );
> > > > value = value.replace(/,**/gi, "");
> > > > value = parseInt(value,10);

> > > } else if( field == "price" ){
> > > > value = value.replace( /[^(\d|\.)]**/gi, "");
> > > > value = value.replace(/,**/gi , "");
> > > > value = parseFloat( value );

> > > }
> > > if( typeof(value) == "number" && isNaN( value ) ){
> > > > error( "Improperly formatted input.");

> > > } else {
> > > > if( typeof( value ) === "string" ){
> > > > > if( value.match(/\~|\=/) ){
> > > > > > error("Special character ~ or = not allowed: " + value);

> > > > > }
> > > > > value = value.replace(/\~|\=/g, "");

> > > > }
> > > > this[field](field.md) = value;
> > > > this.checkQuantityAndPrice();

> > > }

> > } else {
> > > error( "Cannot change " + field + ", this is a reserved field.");

> > }
> > simpleCart.update();

> },

> increment : function(){
> > this.quantity = parseInt(this.quantity,10) + 1;
> > simpleCart.update();

> },

> decrement : function(){
> > if( parseInt(this.quantity,10) < 2 ){
> > > this.remove();

> > } else {
> > > this.quantity = parseInt(this.quantity,10) - 1;
> > > simpleCart.update();

> > }

> },

> print : function () {
> > var returnString = '',
> > > field;

> > simpleCart.each(this ,function(item,x,name){
> > > returnString+= escape(name) + "=" + escape(item) + "||";

> > });
> > return returnString.substring(0,returnString.length-2);

> },


> checkQuantityAndPrice : function() {

> if( !this.quantity | this.quantity == null || this.quantity == 'undefined'){|
|:----------------------|
> > this.quantity = 1;
> > error('No quantity for item.');

> } else {
> > this.quantity = ("" + this.quantity).replace(/,**/gi, "" );
> > this.quantity = parseInt( ("" + this.quantity).replace( /[^(\d|\.)]**/gi, "") , 10);
> > if( isNaN(this.quantity) ){
> > > error('Quantity is not a number.');
> > > this.quantity = 1;

> > }

> }

> if( !this.price | this.price == null || this.price == 'undefined'){|
|:-------------------|
> > this.price=0.00;
> > error('No price for item or price not properly formatted.');

> } else {
> > this.price = ("" + this.price).replace(/,**/gi, "" );
> > this.price = parseFloat( ("" + this.price).replace( /[^(\d|\.)]**/gi, "") );
> > if( isNaN(this.price) ){
> > > error('Price is not a number.');
> > > this.price = 0.00;

> > }

> }
> },


> parseValuesFromArray : function( array ) {
> > if( array && array.length && array.length > 0) {
> > > for(var x=0, xlen=array.length; x<xlen;x++ ){


> /**ensure the pair does not have key delimeters**/
> array[x](x.md) = array[x](x.md).replace(/\|\|/g, "| |");
> array[x](x.md) = array[x](x.md).replace(/\+\+/g, "+ +");
> if( array[x](x.md).match(/\~/) ){
> > error("Special character ~ not allowed: " + array[x](x.md));

> }
> array[x](x.md) = array[x](x.md).replace(/\~/g, "");


> /**split the pair and save the unescaped values to the item**/
> var value = array[x](x.md).split('=');
> if( value.length>1 ){
> > if( value.length>2 ){
> > > for(var j=2, jlen=value.length;j<jlen;j++){
> > > > value[1](1.md) = value[1](1.md) + "=" + value[j](j.md);

> > > }

> > }
> > this[unescape(value[0](.md)).toLowerCase() ] = unescape(value[1](1.md));

> }
> }
> return true;
> } else {
> > return false;

> }
> },

> remove : function() {
> > simpleCart.remove(this.id);
> > simpleCart.update();

> }
};



/
  * Shelf Object for managing items on shelf that can be added to cart
  * /

function Shelf(){
> this.items = {};
}
Shelf.prototype = {

> readPage : function () {
> > this.items = {};
> > var newItems = getElementsByClassName( "simpleCart\_shelfItem" ),
> > > newItem;
> > > me = this;


> for( var x = 0, xlen = newItems.length; x<xlen; x++){
> > newItem = new ShelfItem();
> > me.checkChildren( newItems[x](x.md) , newItem );
> > me.items[newItem.id] = newItem;

> }
> },

> checkChildren : function ( item , newItem) {
> > if( !item.childNodes )
> > > return;

> > for(var x=0;item.childNodes[x](x.md);x++){


> var node = item.childNodes[x](x.md);
> if( node.className && node.className.match(/item_[^ ]+/) ){_

> var data = /item_[^ ]+/.exec(node.className)[0](0.md).split("_");

> if( data[1](1.md) == "add" || data[1](1.md) == "Add" ){
> > var tempArray = [.md](.md);
> > tempArray.push( node );
> > var addFunction = simpleCart.Shelf.addToCart(newItem.id);
> > simpleCart.addEventToArray( tempArray , addFunction , "click");
> > node.id = newItem.id;

> } else {
> > newItem[data[1](1.md)]  = node;

> }
> }
> if( node.childNodes[0](0.md) ){
> > this.checkChildren( node , newItem );

> }
> }
> },

> empty : function () {
> > this.items = {};

> },


> addToCart : function ( id ) {
> > return function(){
> > > if( simpleCart.Shelf.items[id](id.md)){
> > > > simpleCart.Shelf.items[id](id.md).addToCart();

> > > } else {
> > > > error( "Shelf item with id of " + id + " does not exist.");

> > > }

> > };

> }
};


/
  * Shelf Item Object
  * /


function ShelfItem(){
> this.id = "s" + simpleCart.nextId++;
}

ShelfItem.prototype = {

> remove : function () {
> > simpleCart.Shelf.items[this.id] = null;

> },

> addToCart : function () {
> > var outStrings = [.md](.md),
> > > valueString,
> > > field;


> for( field in this ){
> > if( typeof( this[field](field.md) ) !== "function" && field !== "id" ){
> > > valueString = "";


> switch(field){
> > case "price":
> > > if( this[field](field.md).value ){
> > > > valueString = this[field](field.md).value;

> > > } else if( this[field](field.md).innerHTML ) {
> > > > valueString = this[field](field.md).innerHTML;

> > > }
> > > /**remove all characters from price except digits and a period**/
> > > valueString = valueString.replace( /[^(\d|\.)]**/gi , "" );
> > > valueString = valueString.replace( /,**/ , "" );
> > > break;

> > case "image":
> > > valueString = this[field](field.md).src;
> > > break;

> > default:
> > > if( this[field](field.md).value ){
> > > > valueString = this[field](field.md).value;

> > > } else if( this[field](field.md).innerHTML ) {
> > > > valueString = this[field](field.md).innerHTML;

> > > } else if( this[field](field.md).src ){
> > > > valueString = this[field](field.md).src;

> > > } else {
> > > > valueString = this[field](field.md);

> > > }
> > > break;

> }
> outStrings.push( field + "=" + valueString );
> }
> }

> simpleCart.add( outStrings );
> }
};



/
  * Thanks to Peter-Paul Koch for these cookie functions (http://www.quirksmode.org/js/cookies.html)
  * /
function createCookie(name,value,days) {
> if (days) {
> > var date = new Date();
> > date.setTime(date.getTime()+(days\*24\*60\*60\*1000));
> > var expires = "; expires="+date.toGMTString();

> }
> else var expires = "";
> value = value.replace(/\=/g, '~');
> document.cookie = name + "=" + escape(value) + expires + "; path=/";
}

function readCookie(name) {
> var nameEQ = name + "=";
> var ca = document.cookie.split(';');
> for(var i=0;i < ca.length;i++) {
> > var c = ca[i](i.md);
> > while (c.charAt(0)==' ') c = c.substring(1,c.length);
> > if (c.indexOf(nameEQ) === 0){
> > > var value = unescape(c.substring(nameEQ.length, c.length));
> > > return value.replace(/\~/g, '=');

> > }

> }
> return null;
}

function eraseCookie(name) {
> createCookie(name,"",-1);
}


//**/**
> Developed by Robert Nyman, http://www.robertnyman.com
> Code/licensing: http://code.google.com/p/getelementsbyclassname/
**/
var getElementsByClassName = function (className, tag, elm){
> if (document.getElementsByClassName) {
> > getElementsByClassName = function (className, tag, elm) {
> > > elm = elm || document;
> > > var elements = elm.getElementsByClassName(className),
> > > > nodeName = (tag)? new RegExp("\\b" + tag + "\\b", "i") : null,
> > > > returnElements = [.md](.md),
> > > > current;

> > > for(var i=0, il=elements.length; i<il; i+=1){
> > > > current = elements[i](i.md);
> > > > if(!nodeName || nodeName.test(current.nodeName)) {
> > > > > returnElements.push(current);

> > > > }

> > > }
> > > return returnElements;

> > };

> }
> else if (document.evaluate) {
> > getElementsByClassName = function (className, tag, elm) {
> > > tag = tag || "**";
> > > elm = elm || document;
> > > var classes = className.split(" "),
> > > > classesToCheck = "",
> > > > xhtmlNamespace = "http://www.w3.org/1999/xhtml",
> > > > namespaceResolver = (document.documentElement.namespaceURI === xhtmlNamespace)? xhtmlNamespace : null,
> > > > returnElements = [.md](.md),
> > > > elements,
> > > > node;

> > > for(var j=0, jl=classes.length; j<jl; j+=1){
> > > > classesToCheck += "[contains(concat(' ', @class, ' '), ' " + classes[j](j.md) + " ')]";

> > > }
> > > try {
> > > > elements = document.evaluate(".//" + tag + classesToCheck, elm, namespaceResolver, 0, null);

> > > }
> > > catch (e) {
> > > > elements = document.evaluate(".//" + tag + classesToCheck, elm, null, 0, null);

> > > }
> > > while ((node = elements.iterateNext())) {
> > > > returnElements.push(node);

> > > }
> > > return returnElements;

> > };

> }
> else {
> > getElementsByClassName = function (className, tag, elm) {
> > > tag = tag || "**";
> > > elm = elm || document;
> > > var classes = className.split(" "),
> > > > classesToCheck = [.md](.md),
> > > > elements = (tag === "**" && elm.all)? elm.all : elm.getElementsByTagName(tag),
> > > > current,
> > > > returnElements = [.md](.md),
> > > > match;

> > > for(var k=0, kl=classes.length; k<kl; k+=1){
> > > > classesToCheck.push(new RegExp("(^|\\s)" + classes[k](k.md) + "(\\s|$)"));

> > > }
> > > for(var l=0, ll=elements.length; l<ll; l+=1){
> > > > current = elements[l](l.md);
> > > > match = false;
> > > > for(var m=0, ml=classesToCheck.length; m<ml; m+=1){
> > > > > match = classesToCheck[m](m.md).test(current.className);
> > > > > if (!match) {
> > > > > > break;

> > > > > }

> > > > }
> > > > if (match) {
> > > > > returnElements.push(current);

> > > > }

> > > }
> > > return returnElements;

> > };

> }
> return getElementsByClassName(className, tag, elm);
};


/
  * Helpers
  * /


String.prototype.reverse=function(){return this.split("").reverse().join("");};
Number.prototype.withCommas=function(){var x=6,y=parseFloat(this).toFixed(2).toString().reverse();while(x<y.length){y=y.substring(0,x)+","+y.substring(x);x+=4;}return y.reverse();};
Number.prototype.toCurrency=function(){return(arguments[0](0.md)?arguments[0](0.md):"$")+this.withCommas();};


/
  * error management
  * /

function error( message ){
> try{
> > console.log( message );

> }catch(err){
> //	alert( message );
> }
}


var simpleCart = new Cart();

if( typeof jQuery !== 'undefined' ) $(document).ready(function(){simpleCart.initialize();});
else if( typeof Prototype !== 'undefined') Event.observe( window, 'load', function(){simpleCart.initialize();});
else window.onload = simpleCart.initialize;