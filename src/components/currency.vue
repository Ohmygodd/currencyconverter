<template>
  <div id="box" class="container">
    <div id="top" class="row">
      <div class="col-lg-12">
        <p>USD - United States Dollars</p>
      </div>
      <div class="col-lg-6">
        <p>USD</p>
      </div>
      <div class="col-lg-6">
        <input id="currVal" type="number" name="" value="10.00" v-on:change="calculate()">
      </div>
    </div>
    <div id="body" class="row">
      <ul class="convert">{{currency}}</ul>
      <ul id="add" class="add">
        <li class="loading" style="display: none;">load currency rate...</li>
        <li id="plus" class="active">
          <a v-on:click="addCurr = !addCurr">(+) Add More Currencies</a>
        </li>
        <li id="drop" class="dropdown" v-show="addCurr">
          <div class="container">
            <div class="row">
              <div class="col-lg-9">
                 <select id="slccurr" v-on:change="othercurr($event)">
                  <option readonly>Choose Currency</option>
                  <option id="optCAD" value="CAD">CAD</option>
                  <option id="optIDR" value="IDR">IDR</option>
                  <option id="optGBP" value="GBP">GBP</option>
                  <option id="optCHF" value="CHF">CHF</option>
                  <option id="optSGD" value="SGD">SGD</option>
                  <option id="optINR" value="INR">INR</option>
                  <option id="optMYR" value="MYR">MYR</option>
                  <option id="optJPY" value="JPY">JPY</option>
                  <option id="optKRW" value="KRW">KRW</option>
                </select>
              </div>
              <div class="col-lg-3">
                <input id="calc" type="submit" name="Submit">
              </div>
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
  import axios from 'axios';
  var countryarr=[];
  var currtxt;
  export default {
    data(){
      return{
        currency: '',
        addCurr : true,
        rate: ''
      }
    },
    methods: {
      delCurr(x) {
        $('#li'+x).remove();
        var sel = document.getElementById('slccurr');
        var opt = document.createElement('option');
        opt.appendChild(document.createTextNode(x));
        opt.id = "opt"+x;
        opt.value = x;
        sel.appendChild(opt);
      },
      calculate(){
        this.rates("",1);
      },
      othercurr(event) {
        var value = event.target.value;
        document.getElementById("opt"+value).remove();
        this.rates(value,0);
      },
      rates: function(curr,x) {
        if (curr!="") {
          countryarr.push(curr);
        }
        var amount = x;
        if (amount > 0) {
          this.result("",1);
        }else{
          if (curr == 'CAD' || curr == 'IDR' || curr == 'GBP' || curr == 'CHF' || curr == 'SGD' || curr == 'INR' || curr == 'MYR' || curr == 'JPY' || curr == 'KRW') {
            if (curr == 'CAD') {
              currtxt="Canadian Dollar";
            }else if (curr == 'IDR'){
              currtxt="Indonesian Rupiah";
            }else if (curr == 'GBP'){
              currtxt="British Pound";
            }else if (curr == 'CHF'){
              currtxt="Swiss Franc";
            }else if (curr == 'SGD'){
              currtxt="Singapore Dollar";
            }else if (curr == 'INR'){
              currtxt="Indian Rupee";
            }else if (curr == 'MYR'){
              currtxt="Malaysian Ringgit";
            }else if (curr == 'JPY'){
              currtxt="Japanese Yen";
            }else if (curr == 'KRW'){
              currtxt="Sout Korean Won";
            }else {
              currtxt="No Currencies";
            }
            $('.loading').css({'display':'block'});
            axios.get("https://api.exchangeratesapi.io/latest?base=USD&symbols="+curr)
              .then(response => {
                console.log(response.data)
                var html='';
                var rate = response.data.rates;
                var exRate = rate[curr];
                html += '<li id="li'+curr+'">'+
                      '<div class="container">'+
                        '<div id="IDR" class="row">'+
                          '<div id="left" class="container col-lg-10">'+
                            '<div class="row">'+
                              '<div class="col-lg-6">'+
                                '<p>'+curr+'</p>'+
                              '</div>'+
                              '<div class="col-lg-6">'+
                                '<p><span class="finalamount">'+this.result(exRate,0)+'<span></p>'+
                              '</div>'+
                              '<div class="col-lg-12">'+
                                '<p>'+curr+' - '+currtxt+'</p>'+
                              '</div>'+
                              '<div class="col-lg-12">'+
                                '<p>1 USD = '+curr+' '+exRate+'</p>'+
                              '</div>'+
                            '</div>'+
                          '</div>'+
                          '<div id="del" class="col-lg-2">'+
                            '<a v-on:click="delCurr(\''+curr+'\')">(-)</a>'+
                          '</div>'+
                        '</div>'+
                      '</div>'+
                    '</li>'
                $('.loading').css({'display':'none'});
                $('.convert').append(html);
                this.$forceUpdate();
                raddCurr();
              })
              .catch((error) => console.log(error));
          }
        }
      },
      result: function (x,y) {
        var base_amount = $('#currVal').val();
        if (y == 0) {
          var final = base_amount*x;
          return final.toFixed(2);
        }else{
          var c = countryarr.toString();
          axios.get("https://api.exchangeratesapi.io/latest?base=USD&symbols="+c)
            .then(response => {
              for (var i=0; i<countryarr.length; i++) {
                var rate = response.data.rates;
                var exRate = rate[countryarr[i]];
                $("#li"+countryarr[i]+" span.finalamount").text(function() {
                  var final = base_amount*exRate;
                  return final.toFixed(2);
                });
              }
          })
          
        }
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  #box {
    width: 450px;
    margin: 50px auto;
    border: 1px solid #555;}
  #top {
    padding: 15px;
    border-bottom: 1px solid #555;}
  #body ul.convert,
  #body ul.add {
    width: 100%;
    padding: 15px;}
  #body ul.convert li,
  #body ul.add li {
    list-style: none;
    border: 1px solid #555;}
  #body ul.convert li {
    margin-bottom: 20px;}
  #body ul.convert p,
  #body ul.add p {
    margin: 0;}
  #left {
    padding: 15px;}
  #del {
    border-left: 1px solid #555;
    text-align: center;}
  #del a {
    cursor: pointer;
    line-height: 102px;}
  /* ul#add li {
    cursor: pointer;
    display: none;}
  ul#add li.active {
    display: block;} */
  #drop .row div{
    padding: 0;}
  #slccurr,
  #calc {
    width: 100%;
    height: 100%;
    border: none;}
</style>
