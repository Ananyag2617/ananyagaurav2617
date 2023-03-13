---
title: Binary Color
layout: base
description: A Binary Math illustrative application using HTML, Liquid, and JavaScript.
permalink: /frontend/binary
categories: [3.B, 3.C, C4.4]
tags: [html, liquid, javascript]
week: 13
type: pbl
---

<!-- Hack 1: add a character display to text when 8 bits, determine if printable or not printable -->
<!-- Hack 2: change to 24 bits and add a color code and display color when 24 bits, think about display on this one -->
<!-- Hack 3: do your own thing -->


{% assign BITS = 8 %}



<div class="container bg-primary">
    <header class="pb-3 mb-4 border-bottom border-primary text-dark">
        <span class="fs-4">Binary Math with Conversions</span>
    </header>
    <div class="row justify-content-md-center">
        <div class="col-8">
        {% for j in (0..2) %}
            <table class="table">
            <tr id="table">
                <th>Plus</th>
                <th>Binary</th>
                <th>Octal</th>
                <th>Hexadecimal</th>
                <th>Decimal</th>
                <th>Minus</th>
            </tr>
            <tr>
                <!-- 1st table: button that says "+1" -->
                <!-- onclick="add(1)", if change 1 to another number, can change increment -->
                <td><button type="button" id="add1{{ j }}" onclick="add(1, {{ j }})">+1</button></td>
                <td id="binary{{ j }}">00000000</td>
                <td id="octal{{ j }}">0</td>
                <td id="hexadecimal{{ j }}">0</td>
                <td id="decimal{{ j }}">0</td>
                <td><button type="button" id="sub1{{ j }}" onclick="add(-1, {{ j }})">-1</button></td>
            </tr>
            </table>
        </div>
        <div class="col-12">
            {% comment %}Liquid for loop includes last number, thus the Minus{% endcomment %}
            <!-- bits = 7 -->
            {% assign bits = BITS | minus: 1 %}
            <!-- some whitespace for easier viewing--> 
            <!-- 2nd table -->
            <table class="table">
            <!-- Print out value for 2^# on table-->
            <tr>
                {% for i in (0..bits) %}
                <td><p id="binaryNote{{ i }}{{ j }}"></p></td>
                {% endfor %}
            </tr>
            <tr>
                {% comment %}Build many bits{% endcomment %}
                {% for i in (0..bits) %}
                <!-- bulb id = 0-7, post lightbulb off pic -->
                <td><img class="img-responsive py-3" id="bulb{{ i }}{{ j }}" src="{{site.baseurl}}/images/w13Binary-bulbOff.png" alt="" width="40" height="Auto">
                    <!-- what does javascript:toggleBit mean? -->
                    <!-- button for "Turn on" -->
                    <button type="button" id="butt{{ i }}{{ j }}" onclick="javascript:toggleBit({{ i }}, {{ j }})">Turn on</button>
                </td>
                {% endfor %}
            </tr>
            <tr>
                <!-- 2nd table w/ light bulb -->
                {% comment %}Value of bit{% endcomment %}
                {% for i in (0..bits) %}
                <!-- Controls the box that h 0/1 when turn on/off-->
                <!-- value = "0": For "text" attributes, define the default value in the input box -->
                <!-- First box on the left = 0 -->
                <td><input type='text' id="digit{{ i }}{{ j }}" value="0" size="1" readonly></td>
                {% endfor %}
            </tr>
            </table>
        {% endfor %}
        </div>
    </div>
<div style="width:500px;height:100px;solid #000;" id="color"></div>

</div>




<script>


    const BITS = {{ BITS }};
    const MAX = 2 ** BITS - 1;
    const MSG_ON = "Turn on";
    const IMAGE_ON = "{{site.baseurl}}/images/w13Binary-bulbOn.gif";
    const MSG_OFF = "Turn off";
    const IMAGE_OFF = "{{site.baseurl}}/images/w13Binary-bulbOff.png"

    // IMPORTANT: print out value for 2^# in <p> tag
    for (let j = 0; j < 3; j++) {
        for (let i = 0; i < BITS; i++) {
            const binNote = document.getElementById('binaryNote' + i + j);
            binNote.innerHTML = (2**(BITS - 1 - i));
        }
    }

    
    

    // return string with current value of each bit
    function getBits(j) {
        let bits = "";
        for(let i = 0; i < BITS; i++) {
            // IMPORTANT: digit = last box w/ "0"
            // JS does auto type conversion
            // return in binary string
            bits = bits + document.getElementById('digit' + i + j).value;
        }
        return bits;
    }
    // setter for DOM values
    function setConversions(binary, j) {
        document.getElementById('binary' + j).innerHTML = binary;
        // Octal conversion
        // IMPORTANT: parseInt: Change binary string to base 10
        // https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/toString
        // toString: if number in front, c use radix (base 8 in this case)
        // https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/toString
        document.getElementById('octal' + j).innerHTML = parseInt(binary, 2).toString(8);
        // Hexadecimal conversion
        document.getElementById('hexadecimal' + j).innerHTML = parseInt(binary, 2).toString(16);
        // Decimal conversion
        document.getElementById('decimal' + j).innerHTML = parseInt(binary, 2).toString();

        const test = document.getElementById('decimal' + 0).innerHTML; 
        const test1 = document.getElementById('decimal' + 1).innerHTML;
        const test2 = document.getElementById('decimal' + 2).innerHTML;
        document.getElementById("color").style.backgroundColor = 'rgb(' + test + ',' + test1 + ',' + test2 + ')';
         
        
    }
    //
    function decimal_2_base(decimal, base) {
        let conversion = "";
        // loop to convert to base
        do {
        let digit = decimal % base;
        conversion = "" + digit + conversion; // what does this do?
        // IMPORTANT: same as Math.floor() (but faster), returns largest integer <= provided #
        decimal = ~~(decimal / base);         // what does this do?
        } while (decimal > 0);                  // why while at the end? what is ~~?
        // loop to pad with zeros
        if (base === 2) {                        // only pad for binary conversions
        for (let i = 0; conversion.length < BITS; i++) {
            conversion = "0" + conversion;
        }
        }
        return conversion;
    }

    // toggle selected bit and recalculate
    function toggleBit(i, j) {
        //alert("Digit action: " + i );

        // IMPORTANT: Get the ID of each below (ID syntax like: ex: img#bulb.img-responsive.py-3
        // or p#test)
        // This ID refers to a specific element that matches the ID
        // https://developer.mozilla.org/en-US/docs/Web/API/Document/getElementById
        const dig = document.getElementById('digit' + i + j);
        const image = document.getElementById('bulb' + i + j);
        const butt = document.getElementById('butt' + i + j);

        // Change digit and visual

        // IMPORTANT: Change to off when bulb on
        if (image.src.match(IMAGE_ON)) {
            // IMPORTANT: change value of last box (w/ 0) to 0
            dig.value = 0;
            image.src = IMAGE_OFF;
            butt.innerHTML = MSG_ON;
        } else {
            // IMPORTANT: change value of last box (w/ 0) to 1
            // Change to on when bulb off
            dig.value = 1;
            image.src = IMAGE_ON;
            butt.innerHTML = MSG_OFF;
        }
        // Binary numbers
        const binary = getBits(j);
        setConversions(binary, j);
    }
    // add is positive integer, subtract is negative integer
    function add(n, j) {
        let binary = getBits(j);
        // convert to decimal and do math
        let decimal = parseInt(binary, 2);
        if (n > 0) {  // PLUS
        // IMPORTANT: MAX === decimal? if true, decimal = 0, if false, decimal += n
        // c also do:  decimal = MAX === decimal ? decimal = 0 : decimal += n;
        // https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator#:~:text=The%20conditional%20(ternary)%20operator%20is,if%20the%20condition%20is%20falsy.
        decimal = MAX === decimal ? 0 : decimal += n; // OVERFLOW or PLUS
        } else  {     // MINUS
        decimal = 0 === decimal ? MAX : decimal += n; // OVERFLOW or MINUS
        }
        // convert the result back to binary
        binary = decimal_2_base(decimal, 2);
        // update conversions
        setConversions(binary, j);
        // update bits
        for (let i = 0; i < binary.length; i++) {
        // IMPORTANT: substr(strat index, end index), inclusive, EXCEPT WHEN start index = 0, then end at end index - 1
            let digit = binary.substr(i, 1);
            document.getElementById('digit' + i + j).value = digit;
            if (digit === "1") {
                document.getElementById('bulb' + i + j).src = IMAGE_ON;
                document.getElementById('butt' + i + j).innerHTML = MSG_OFF;
            } else {
                document.getElementById('bulb' + i + j).src = IMAGE_OFF;
                document.getElementById('butt' + i + j).innerHTML = MSG_ON;
            }
        }
    }

    
</script>
