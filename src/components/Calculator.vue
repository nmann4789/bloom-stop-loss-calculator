<template>
  <div>
    <div class="calculator">
      <div class="form-header">
        <h2>Stop Loss Calculator</h2>
        <h3>Are you self insured or fully insured?</h3>
      </div>
      <form v-on:submit.prevent="handleInputsNext" id="calculator-form" v-bind:class="{ 'hidden': bShowPersonalInformation }">
        <div>
          <div v-on:click="setSelfInsured(input, $event)" class="insurance-type">Self Insured</div>
          <div v-on:click="setFullyInsured(input, $event)" class="insurance-type">Fully Insured</div>
        </div>
        <div v-if="bShowInputs">
          <label for="employeeLives">Employee Lives:</label>
          <input required type="number" v-model="employeeLives.formInput" name="employeeLives" placeholder="Enter Number">
        </div>
        <div v-if="!bSelfInsured && bShowInputs">
          <label for="annualPremium">Annual Premium:</label>
          <input required type="number" v-model="annualPremium.formInput" name="annualPremium" placeholder="Enter Cost ($)">
        </div>
        <div v-if="bSelfInsured && bShowInputs">
          <label for="annualWorkingFund">Annual Working Fund:</label>
          <input requried type="number" v-model="annualWorkingFund.formInput" name="annualWorkingFund" placeholder="Enter Cost ($)">
        </div>
        <div v-if="bSelfInsured && bShowInputs">
          <label for="annualAdminCost">Annual Administrative Cost:</label>
          <input required type="number" v-model="annualAdminCost.formInput" name="annualAdminCost" placeholder="Enter Cost ($)">
        </div>
        <div v-if="bSelfInsured && bShowInputs">
          <label for="annualStopLossPremium">Annual Stop Loss Premium:</label>
          <input required type="number" v-model="annualStopLossPremium.formInput" name="annualStopLossPremium" placeholder="Enter Cost ($)">
        </div>
        <div>
          <label></label>
          <input type="submit" class="submit" :value="bPardotSubmitted ? 'Calculate' : 'Next' "/>
        </div>
      </form>
      <form accept-charset="UTF-8" method="post" action="http://ww2.springgroup.com/l/147001/2019-06-18/5k9xbn" class="form" id="pardot-form" v-on:submit.prevent="calculateSavings" v-bind:class="{ 'hidden': !bShowPersonalInformation }">
        <div class="form-field  first_name pd-text required    ">
          <label class="field-label" for="147001_87423pi_147001_87423">First Name</label>
          <input type="text" name="147001_87423pi_147001_87423" id="147001_87423pi_147001_87423" value="Nicholas" class="text" size="30" maxlength="40" onchange="">
        </div>
        <div class="form-field  last_name pd-text required    ">
          <label class="field-label" for="147001_87425pi_147001_87425">Last Name</label>
          <input type="text" name="147001_87425pi_147001_87425" id="147001_87425pi_147001_87425" value="Mann" class="text" size="30" maxlength="80" onchange="">
        </div>
        <div class="form-field  company pd-text required    ">
          <label class="field-label" for="147001_87427pi_147001_87427">Company</label>
          <input type="text" name="147001_87427pi_147001_87427" id="147001_87427pi_147001_87427" value="TEST" class="text" size="30" maxlength="255" onchange="">
        </div>
        <div class="form-field  email pd-text">
          <label class="field-label" for="147001_87429pi_147001_87429">Email</label>
          <input type="text" name="147001_87429pi_147001_87429" id="147001_87429pi_147001_87429" value="nmann4789@gmail.com" class="text" size="30" maxlength="255" required>
        </div>
        <div class=" ">
          <label class="field-label" for="147001_87431pi_147001_87431">Job Title</label>
          <input type="text" name="147001_87431pi_147001_87431" id="147001_87431pi_147001_87431" value="Web Developer" class="text" size="30" maxlength="128" onchange="" required>
        </div>
        <div>
          <label v-on:click="showInputs" style="text-transform: none; cursor:pointer">Back</label>
          <input type="submit" accesskey="s" class="submit" value="Calculate"/>
        </div>
      </form>
    </div>
    <div class="results" v-if="showResults">
      <div class="fully-insured-results projected-bloom-savings" v-if="!bSelfInsured">
        <div class="table">
          <div class="table-header">Projected Bloom Savings</div>
          <div class="table-subheader">Self Insured</div>
          <div class="table-row">
            <div>Premium Savings (%)</div>
            <div>{{parseFloat(this.results.fullyInsuredInfo.premiumSavingsPercent*100).toFixed(2)}}%</div>
          </div>
          <div class="table-row total">
            <div>Annual Premium Savings ($)</div>
            <div>${{parseFloat(this.results.fullyInsuredInfo.annualPremiumSavings).toFixed(2)}}</div>
          </div>
        </div>
        <div class="table">
          <div class="table-header">
            <div>Field</div>
            <div>Provided Input</div>
            <div>Assumptions (Current)</div>
            <div>Assumptions (Bloom)</div>
          </div>
          <div class="table-subheader">Fully Insured</div>
          <div class="table-row row-separator">
            <div>Employee Lives</div>
            <div>{{this.employeeLives.formInput}}</div>
            <div>{{this.employeeLives.formInput}}</div>
            <div>{{this.employeeLives.formInput}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Premium</div>
            <div>${{parseFloat(this.annualPremium.formInput/12).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualPremium/12).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyPremium).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Premium</div>
            <div>${{parseFloat(this.annualPremium.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualPremium).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.annualPremium).toFixed(2)}}</div>
          </div>
          <div class="table-row row-separator">
            <div>$PEPM Premium</div>
            <div>${{parseFloat(this.calculated.monthlyPremium/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.monthlyPremium/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyPremium/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Administrative Cost</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.assumptions.selfInsuredInfo.adminAnnualCost/12).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Administrative Cost</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.assumptions.selfInsuredInfo.adminAnnualCost).toFixed(2)}}</div>
          </div>
          <div class="table-row row-separator">
            <div>$PEPM Administrative Cost</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.assumptions.selfInsuredInfo.adminAnnualCost/12/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Stop Loss Premium</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.results.bloom.monthlyStopLossPremium).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Stop Loss Premium</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.results.bloom.annualStopLossPremium).toFixed(2)}}</div>
          </div>
          <div class="table-row row-separator">
            <div>$PEPM Stop Loss Premium</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.calculated.monthlyStopLossPremium/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Claims Cost</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.results.bloom.monthlyClaimsCost).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Claims Cost</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.results.bloom.annualClaimsCost).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>$PEPM Claims Cost</div>
            <div>N/A</div>
            <div>N/A</div>
            <div>${{parseFloat(this.calculated.monthlyClaimsPick/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
        </div>
      </div>
      <div class="self-insured-results projected-bloom-savings" v-if="bSelfInsured">
        <div class="table">
          <div class="table-header">Projected Bloom Stop-Loss Savings for Entity X</div>
          <div class="table-subheader">Self Insured</div>
          <div class="table-row">
            <div>Stop Loss Premium Savings (% of SL Premium)</div>
            <div>{{this.assumptions.selfInsuredInfo.stopLossPercentSavings*100}}%</div>
          </div>
          <div class="table-row total">
            <div>Annual Stop Loss Premium Savings ($)</div>
            <div>${{parseFloat(this.results.selfInsuredInfo.annualStopLossPremiumSavings).toFixed(2) }}</div>
          </div>
        </div>
      </div>
      <div class="self-insured-results projected-bloom-savings" v-if="bSelfInsured">
        <div class="table">
          <div class="table-header">Potential Bloom Additional Savings for Entity X</div>
          <div class="table-row">
            <div>Admin Savings (% of Admin Costs)</div>
            <div>{{this.results.bloom.adminSavingsPercentOfAdminCosts*100}}%</div>
          </div>
          <div class="table-row total">
            <div>Annual Admin Savings ($)</div>
            <div>${{parseFloat(this.results.bloom.adminAnnualSavings).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Rx Savings via Carve-Out (% of Claims)</div>
            <div>{{parseFloat(this.results.bloom.rxSavingsViaCarveOut).toFixed(2)}}%</div>
          </div>
          <div class="table-row total">
            <div>Annual Rx Savings via Carve-Out ($)</div>
            <div>${{parseFloat(this.results.bloom.annualRxSavingsViaCarveOut).toFixed(2)}}</div>
          </div>
          <div class="table-row total additional-savings">
            <div>Total Additional Potential Savings</div>
            <div>${{parseFloat(this.results.bloom.totalAdditionalPotentialSavings).toFixed(2)}}</div>
          </div>
          <div class="table-row total possible-savings">
            <div>Total Possible Savings</div>
            <div>${{parseFloat(this.results.bloom.totalPossibleSavings).toFixed(2)}}</div>
          </div>
        </div>
        <div class="table">
          <div class="table-header">
            <div>Field</div>
            <div>Provided Input</div>
            <div>Assumptions (Current)</div>
            <div>Assumptions (Bloom)</div>
          </div>
          <div class="table-subheader">Fully Insured</div>
          <div class="table-row row-separator">
            <div>Employee Lives</div>
            <div>{{this.employeeLives.formInput}}</div>
            <div>{{this.employeeLives.formInput}}</div>
            <div>{{this.employeeLives.formInput}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Working Fund</div>
            <div>${{parseFloat(this.annualWorkingFund.formInput/12).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualWorkingFund/12).toFixed(2)}}</div>
            <div>{{parseFloat(this.results.bloom.monthlyWorkingFund).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Working Fund</div>
            <div>${{parseFloat(this.annualWorkingFund.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualWorkingFund).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.annualWorkingFund).toFixed(2)}}</div>
          </div>
          <div class="table-row row-separator">
            <div>$PEPM Premium</div>
            <div>${{parseFloat(this.calculated.monthlyWorkingFund/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.monthlyWorkingFund/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyWorkingFund/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Administrative Cost</div>
            <div>${{parseFloat(this.annualAdminCost.formInput/12).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualAdminCost/12).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyAdminCost).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Administrative Cost</div>
            <div>${{parseFloat(this.annualAdminCost.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualAdminCost).toFixed(2)}}</div>
            <div>${{(this.results.bloom.annualAdminCost).toFixed(2)}}</div>
          </div>
          <div class="table-row row-separator">
            <div>$PEPM Administrative Cost</div>
            <div>${{parseFloat(0.00).toFixed(2)}}</div>
            <div>${{parseFloat((this.calculated.annualAdminCost/12)/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.assumptions.selfInsuredInfo.adminAnnualCost/12/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Stop Loss Premium</div>
            <div>${{parseFloat(this.annualStopLossPremium.formInput/12).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.monthlyStopLossPremium).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyStopLossPremium).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Stop Loss Premium</div>
            <div>${{parseFloat(this.annualStopLossPremium.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualStopLossPremium).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.annualStopLossPremium).toFixed(2)}}</div>
          </div>
          <div class="table-row row-separator">
            <div>$PEPM Stop Loss Premium</div>
            <div>${{parseFloat(0.00).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.monthlyStopLossPremium/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyStopLossPremium/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Monthly Claims Cost</div>
            <div>${{parseFloat(0.00).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.monthlyClaimsPick).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyClaimsCost).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>Annual Claims Cost</div>
            <div>${{parseFloat(0.00).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.annualClaimsPick).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.annualClaimsCost).toFixed(2)}}</div>
          </div>
          <div class="table-row">
            <div>$PEPM Claims Cost</div>
            <div>${{parseFloat(this.annualAdminCost.formInput/12/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.calculated.monthlyClaimsPick/this.employeeLives.formInput).toFixed(2)}}</div>
            <div>${{parseFloat(this.results.bloom.monthlyClaimsCost/this.employeeLives.formInput).toFixed(2)}}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>

import JQuery from 'jquery'
const axios = require('axios');
const jsonpAdapter = require('axios-jsonp');
let $ = JQuery

export default {
  name: 'Calculator',
  data () {
    return {
      bPreviousResults:false,
      showResults:false,
      bShowInputs:false,
      bShowPersonalInformation:false,
      bPardotSubmitted:false,
      bSelfInsured:false,
      shareLink:'',
      assumptions:{
        fullyInsuredInfo:{
          fullyInsuredTotalCost:1650,
          fiPremiumBasedOnLives:0,
          fISavingsHIT:0.022,
          fISavingsRx:0.0432,
          fISavingsBaseline:0,
          fISavings:0,
          fIPremiumSavingsEstimate:0,
          fIAnnualPremium:0
        },
        additionalToMatchSIBloomCost:0.009,
        selfInsuredInfo:{
          selfInsuredTotalCost:1570,
          siPemBasedOnLives:0,
          projectedCurrentAdmin:60,
          adminPercentSavingsDefault:0,
          bloomAdminPEMP:45,
          adminAnnualCost:0,
          pmbSavings:0,
          stopLossPercentSavings:0.10,
          slPercentOfWorkingBasedOnSize:0,
          standardEstimateSLPercentOfWork:0.10,
        }
      },
      calculated:{
        monthlyPremium:0,
        annualPremium:0,
        monthlyWorkingFund:0,
        annualWorkingFund:0,
        monthlyAdminCost:0,
        annualAdminCost:0,
        monthlyStopLossPremium:0,
        annualStopLossPremium:0,
        monthlyClaimsPick:0,
        annualClaimsPick:0
      },
      results:{
        fullyInsuredInfo:{
          premiumSavingsPercent:0,
          annualPremiumSavings:0
        },
        selfInsuredInfo:{
          stopLossPremiumSavingsPercent:0,
          annualStopLossPremiumSavings:0
        },
        bloom:{
          adminSavingsPercentOfAdminCosts:0,
          adminAnnualSavings:0,
          rxSavingsViaCarveOut:0,
          annualRxSavingsViaCarveOut:0,
          totalAdditionalPotentialSavings:0,
          totalPossibleSavings:0,
          monthlyPremium:0,
          annualPremium:0,
          monthlyAdminCost:0,
          annualAdminCost:0,
          monthlyWorkingFund:0,
          annualWorkingFund:0,
          monthlyStopLossPremium:0,
          annualStopLossPremium:0,
          monthlyClaimsCost:0,
          annualClaimsCost:0
        }
      },
      membersPerEmployee:2.2,
      employeeLives:{
        formInput:""
      },
      annualPremium:{
        formInput:0
      },
      annualWorkingFund:{
        formInput:0
      },
      annualAdminCost:{
        formInput:0
      },
      annualStopLossPremium:{
        formInput:0
      },
    }
  },
  created () {

  },
  mounted () {
    this.checkForExistingResults()
    this.setTotalProfits()
    if(this.bPreviousResults){
      this.calculateSavings()
    }
  },
  methods: {
    setSelfInsured: function (input, event) {
      this.bSelfInsured = true
      this.bShowInputs = true
      $(event.target).siblings().removeClass('active')
      $(event.target).addClass('active')
    },
    setFullyInsured: function (input, event) {
      this.bSelfInsured = false
      this.bShowInputs = true
      $(event.target).siblings().removeClass('active')
      $(event.target).addClass('active')
    },
    getParameterByName: function (name) {
      name = name.replace(/[[]/, '\\[').replace(/[\]]/, '\\]')
      let regex = new RegExp('[\\?&]' + name + '=([^&#]*)')
      let results = regex.exec(window.location.search)
      if(results == null){
        console.log("setting share link to false")
        this.bPreviousResults = false
      }
      return (results === null) ? '' : decodeURIComponent(results[1].replace(/\+/g, ' '))
    },
    formatMoney: function (number, decPlaces, decSep, thouSep) {
      decPlaces = isNaN(decPlaces = Math.abs(decPlaces)) ? 2 : decPlaces,
        decSep = typeof decSep === "undefined" ? "." : decSep;
      thouSep = typeof thouSep === "undefined" ? "," : thouSep;
      var sign = number < 0 ? "-" : "";
      var i = String(parseInt(number = Math.abs(Number(number) || 0).toFixed(decPlaces)));
      var j = (j = i.length) > 3 ? j % 3 : 0;

      return sign +
        (j ? i.substr(0, j) + thouSep : "") +
        i.substr(j).replace(/(\decSep{3})(?=\decSep)/g, "$1" + thouSep) +
        (decPlaces ? decSep + Math.abs(number - i).toFixed(decPlaces).slice(2) : "");
    },
    initAssumptions: function() {
      //Self Insured Assumptions
      this.lookupSavings(parseInt(this.employeeLives.formInput));

      if(!this.bSelfInsured){
        this.annualWorkingFund.formInput = this.annualPremium.formInput
      }

      this.assumptions.selfInsuredInfo.siPemBasedOnLives = this.employeeLives.formInput*this.assumptions.selfInsuredInfo.selfInsuredTotalCost*this.membersPerEmployee*12
      this.assumptions.selfInsuredInfo.pmbSavings = this.assumptions.fullyInsuredInfo.fISavingsRx
      this.assumptions.selfInsuredInfo.adminAnnualCost = this.employeeLives.formInput*this.assumptions.selfInsuredInfo.bloomAdminPEMP*12
      this.assumptions.selfInsuredInfo.slPercentOfWorkingBasedOnSize = 3.2094 * Math.pow(this.employeeLives.formInput, -0.569)
      this.assumptions.selfInsuredInfo.adminPercentSavingsDefault = (this.assumptions.selfInsuredInfo.projectedCurrentAdmin/this.assumptions.selfInsuredInfo.bloomAdminPEMP)-1

      this.calculated.annualWorkingFund = (this.annualWorkingFund.formInput > 0) ? this.annualWorkingFund.formInput : this.assumptions.selfInsuredInfo.siPemBasedOnLives
      this.calculated.monthlyWorkingFund = this.calculated.annualWorkingFund/12

      this.calculated.annualAdminCost = (this.annualAdminCost.formInput > 0) ? this.annualAdminCost.formInput : this.assumptions.selfInsuredInfo.projectedCurrentAdmin*this.employeeLives.formInput*12
      this.calculated.monthlyAdminCost = this.calculated.annualAdminCost/12

      this.calculated.annualStopLossPremium = (this.annualStopLossPremium.formInput > 0) ? this.annualStopLossPremium.formInput : this.assumptions.selfInsuredInfo.slPercentOfWorkingBasedOnSize*this.calculated.annualWorkingFund
      this.calculated.monthlyStopLossPremium = this.calculated.annualStopLossPremium/12

      this.calculated.annualClaimsPick = this.calculated.annualWorkingFund-this.calculated.annualAdminCost-this.calculated.annualStopLossPremium
      this.calculated.monthlyClaimsPick = this.calculated.annualClaimsPick/12

      this.results.selfInsuredInfo.annualStopLossPremiumSavings = this.calculated.annualStopLossPremium*this.assumptions.selfInsuredInfo.stopLossPercentSavings

      this.results.bloom.adminSavingsPercentOfAdminCosts = 1 - (this.assumptions.selfInsuredInfo.adminAnnualCost/this.calculated.annualAdminCost)
      this.results.bloom.adminAnnualSavings = this.calculated.annualAdminCost - this.assumptions.selfInsuredInfo.adminAnnualCost
      this.results.bloom.rxSavingsViaCarveOut = this.assumptions.selfInsuredInfo.pmbSavings*100
      this.results.bloom.annualRxSavingsViaCarveOut = this.assumptions.selfInsuredInfo.pmbSavings*this.calculated.annualClaimsPick
      this.results.bloom.totalAdditionalPotentialSavings = this.results.bloom.adminAnnualSavings+this.results.bloom.annualRxSavingsViaCarveOut
      this.results.bloom.totalPossibleSavings = this.results.bloom.totalAdditionalPotentialSavings+this.results.selfInsuredInfo.annualStopLossPremiumSavings

      this.results.bloom.monthlyAdminCost = (1-this.results.bloom.adminSavingsPercentOfAdminCosts)*(this.calculated.annualAdminCost/12)
      this.results.bloom.annualAdminCost = this.results.bloom.monthlyAdminCost*12

      this.results.bloom.monthlyStopLossPremium = (1-this.assumptions.selfInsuredInfo.stopLossPercentSavings)*this.calculated.monthlyStopLossPremium
      this.results.bloom.annualStopLossPremium = this.results.bloom.monthlyStopLossPremium*12

      this.results.bloom.monthlyClaimsCost = (1-this.assumptions.selfInsuredInfo.pmbSavings)*this.calculated.monthlyClaimsPick
      this.results.bloom.annualClaimsCost = (1-this.assumptions.selfInsuredInfo.pmbSavings)*this.calculated.monthlyClaimsPick*12

      if(this.bSelfInsured){
        this.results.bloom.monthlyWorkingFund = this.results.bloom.monthlyAdminCost+this.results.bloom.monthlyStopLossPremium+this.results.bloom.monthlyClaimsCost
        this.results.bloom.annualWorkingFund = this.results.bloom.annualAdminCost+this.results.bloom.annualStopLossPremium+this.results.bloom.annualClaimsCost

      } else {
        //Fully Insured Assumptions
        this.calculated.annualPremium = this.calculated.annualWorkingFund
        this.calculated.monthlyPremium = this.calculated.annualPremium/12

        this.results.bloom.monthlyPremium = this.results.bloom.monthlyAdminCost+this.results.bloom.monthlyStopLossPremium+this.results.bloom.monthlyClaimsCost
        this.results.bloom.annualPremium = this.results.bloom.annualAdminCost+this.results.bloom.annualStopLossPremium+this.results.bloom.annualClaimsCost

        this.results.fullyInsuredInfo.annualPremiumSavings = this.calculated.annualPremium - this.results.bloom.annualPremium
        this.results.fullyInsuredInfo.premiumSavingsPercent = this.results.fullyInsuredInfo.annualPremiumSavings/this.calculated.annualPremium

      }

    },
    lookupSavings: function(employeeCount) {
      if (employeeCount < 400) {
        this.assumptions.fullyInsuredInfo.fISavings = 0.03;
      }else if (employeeCount < 750) {
        this.assumptions.fullyInsuredInfo.fISavings = 0.025;
      }else if (employeeCount < 1000){
        this.assumptions.fullyInsuredInfo.fISavings = 0.02;
      }else if (employeeCount < 1500){
        this.assumptions.fullyInsuredInfo.fISavings = 0.017;
      }else if (employeeCount < 2000){
        this.assumptions.fullyInsuredInfo.fISavings = 0.015;
      }else if (employeeCount < 2500){
        this.assumptions.fullyInsuredInfo.fISavings = 0.013;
      }else if (employeeCount < 3000){
        this.assumptions.fullyInsuredInfo.fISavings = 0.011;
      }else{
        this.assumptions.fullyInsuredInfo.fISavings = 0.01;
      }
    },
    checkForExistingResults : function (){

    },
    handleInputsNext () {
      this.initAssumptions()
      if(this.bPardotSubmitted){
        this.calculateSavings()
      }else{
        this.showPardotForm()
      }
    },
    showPardotForm () {
      this.bShowPersonalInformation = true
    },
    calculateSavings: function (event){
      let form = $('#pardot-form')
      axios({
        headers: {"Access-Control-Allow-Origin": "*"},
        method: 'post',
        url: form.attr('action'),
        data: {
          '147001_87423pi_147001_87423': 'Fred',
          '147001_87425pi_147001_87425': 'Flintstone',
          '147001_87427pi_147001_87427': 'Test Company',
          '147001_87429pi_147001_87429': 'straubme@gmail.com',
          '147001_87431pi_147001_87431': 'job title'
        },
        adapter: jsonpAdapter,
        callbackParamName: 'c' // optional, 'callback' by default
      }).then((res) => {
        console.log(res)
      });
      this.showResults = true
      //this.setShareLink()
      this.bPardotSubmitted = true
    },
    convertToPercentage: function (decimal){
      return decimal*100+"%"
    },
    setTotalProfits: function () {
      },
    showInputs () {
      this.bShowPersonalInformation = false
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="less">
  .calculator{
    width:500px;
    margin:0 auto 30px;
    padding:30px;
    border:1px solid #efefef;
    border-radius: 5px;
    position: relative;
    overflow: hidden;
    min-height:360px;
    form{
      transition: 1s all;
      &#pardot-form{
        position: absolute;
        left:30px;
        width:calc(100% - 60px);
        top:130px;
        opacity: 1;
        &.hidden{
          left: 100% !important;
          opacity: 0;
        }
      }
      &#calculator-form{
        transition:1s all;
        position: relative;
        left:0;
        opacity: 1;
        &.hidden{
          left:-560px;
          opacity: 0;
        }
      }
      div{
        margin-bottom:10px;
        display: flex;
        align-items: center;
        justify-content: space-between;
        label{
          font-size: 12px;
          text-transform: uppercase;
          color:#999;
        }
        input{
          -webkit-appearance: none;
          -moz-appearance: none;
          appearance: none;
          outline:0;
          border:1px solid #efefef;
          border-radius: 4px;
          padding:10px;
          position: relative;
        }
        .submit{
          -webkit-appearance: none;
          -moz-appearance: none;
          appearance: none;
          outline:0;
          border-radius: 4px;
          padding:10px;
          position: relative;
          width: 147px;
          font-weight: bold;
          color:white;
          background-color: #2583e3;
          cursor:pointer;
        }
      }
    }
    .form-header{
      h2{
        width:100%;
        text-align: center;
        margin:0 0 15px;
      }
      h3{
        text-align: center;
      }
      p{
        font-size: 12px;
        font-style: italic;
        color:#777;
        margin:0 0 20px;
      }
    }
  }
  .results{
    width:1000px;
    margin:0 auto;
    >div{
      margin-bottom: 30px;
      border-radius: 5px;
    }
    .total-savings{
      text-align: center;
      margin-bottom: 30px;
      span{
        color: #85bb65;
      }
    }
    .breakdown{
      ul{
        padding-left: 20px;
      }
    }
    .notes{
      font-size: 14px;
      font-style: italic;
    }
    .table{
      margin-bottom: 20px;
      border:1px solid #efefef;
      >div{
        display: flex;
        align-items: center;
        justify-content: space-between;
        &:not(.table-header){
          >div{
            padding:10px ;
          }
        }
        >div{
          width: 25%;
          &:not(:first-child){
            text-align: right;
          }
          &:not(:last-child){
            border-right:1px solid #efefef;
          }
        }
        &:not(:first-child){
          border-bottom: 2px solid #f2f2f2;
        }
      }
      .table-header{
        background-color: #f2f2f2;
        text-align: center;
        font-weight: bold;
        justify-content: space-between;
        padding:10px ;
        >div{
          &:first-child{
            text-align: left;
          }
        }
      }
      .table-subheader{
        background-color: #EAF0FE;
        font-weight: bold;
        padding:10px ;
      }
      .total{
        background-color: #8EE8B8;
        font-weight: bold;
        &.additional-savings{
          background-color: orange;
        }
        &.possible-savings{
          background-color: lightsteelblue;
        }
      }
    }
  }
  .row-separator{
    border-bottom: 2px solid #bdbdbd !important;
  }
  .insurance-type{
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance:none;
    padding:9px;
    background-color: white;
    border:1px solid #2583e3;
    color:#2583e3;
    outline:none;
    border-radius: 3px;
    cursor:pointer;
    font-size: 12px;
    &.active{
      background-color: #2583e3;
      color:white;
      padding:10px;
    }
  }
</style>
