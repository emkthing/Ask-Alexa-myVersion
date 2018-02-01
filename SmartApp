definition(
	name: "Asker",
	namespace: "Tek",
	author: "me",
	description: "Turns on and off a collection of lights based on the state of a specific switch.",
	category: "Convenience",
    iconUrl: "https://raw.githubusercontent.com/n8xd/AskHome/master/askhome108.png",
    iconX2Url: "https://raw.githubusercontent.com/n8xd/AskHome/master/askhome512.png"
)

preferences {
	page(name: "connectDevPage")
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////

// Use inputs to attach smartthings devices to this app
def connectDevPage() {
   dynamicPage(name: "connectDevPage", title:"Connect Devices", input: true, uninstall: true ) {
      section(title: "Select Devices") {
        input "brlight", "capability.switch", title: "Select the Bedroom Light", required: true, multiple:false
      }
      if (!state.tok) { try { state.tok = createAccessToken()} catch (error) {state.tok = null }}
      section(title: "Show the OAUTH ID/Token Pair") {
        paragraph "   var STappID = '${app.id}';\n   var STtoken = '${state.tok}';\n"
      }
      section([mobileOnly:true]) {
		label title: "Assign a name", required: false
      }
   }
}

/////////////////////////////////////////////////////////////////////////////////////////////////////////
mappings { path("/:noun/:operator/:operand/:inquiz"){ action: [GET: "centralCommand"] } }
/////////////////////////////////////////////////////////////////////////////////////////////////////////

def centralCommand() {
        log.debug params

	def noun = params.noun
        def op  = params.operator 
        def opa = params.operand   
        def inq = params.inquiz    
        
        log.debug "Central Command  ${noun} ${op} ${opa} ${inq}"
        
        state.talk2me = ""    
        
        // adjust for the english language, if the user uses inquiry words, then switch things to status in select cases
        // ask home if something is on  -- is really a status check, not command to change something
        // ask home how hot is the basement -- is really a temperature check
        // so if there is an inquisitor, then check the status instead of doing a command.
       
        if (op == "none") { op = "status" }                      //if there is no op, status request
        if (["done","finished"].contains(op)) { op = "status" }  //with or without inquisitor these are status
        if (inq != "none") {                                     //with an inquisitor these are status
            if (["on","off","open","close"].contains(op)) { op = "status" }
        }

        // nouns - persons places and things, based on which noun you want to work with, and which operation
        // you want to perform...take some actions on smartthings devices and their capabilities.
        // make sure you put your nouns in the Alexa Developer part under LIST_OF_NOUNS, same for any new
        // operations you make up...remember you can say "pop" and "cap" for open and closed if you
        // put them in the LIST_OF_OPERATORS and list it in the "op" cases below.
        // you can also use on and off with open and close devices...turn the water off (instead of open the city water valve)
        // simply by including them in the op cases.  Adjust the capability to recognize the word, or make a new capability
        // that does the same thing, with the alternate operators and call it, instead.


        switch (noun) {
            case "light"       :  switch(op) {       // simple on and off
                                            case "on"        :
                                            case "off"       : 
                                            case "open"		 :
                                            case "close"	 :
                                            case "status"    : switchResponse(brlight,noun,op); break
                                            default          : defaultResponseUnkOp(noun,op)
                                          }
                                          break
                                          
            case "none"                :  defaultResponseWhat()
                                          break
                                          
            default                    :  defaultResponseUnkNoun(noun,op)
      }
      
      return ["talk2me" : state.talk2me]
}



//////////////////////////////////////////////////////////////////////////////////////////////////////////
//capability responses - DO and Report
//
//////////////////////////////////////////////////////////////////////////////////////////////////////////

def defaultResponseWhat()
{
      state.talk2me = state.talk2me + "Ask me about something, or to do something with something.   " 
}

// defaultResponse Unknown Device
def defaultResponseUnkNoun(noun, op)
{
      state.talk2me = state.talk2me + "I can't find an person, place, or thing called ${noun} in the smart app.  " 
}


// defaultResponse Unknown Operator for device
def defaultResponseUnkOp(noun, op)
{
      state.talk2me = state.talk2me + "I haven't been told how to do ${op} with ${noun} yet.  "
}

def switchResponse(handle, noun, op)
{
      def arg = handle.currentValue("switch")                        						//value before change 
          if ((op == "on") || (op == "open")) { onMethod(); arg = "turning " + op;}     	//switch flips slow in state, so tell them we did Op
          else if ((op == "off") || (op = "close")) { offMethod(); arg = "turning " + op; } // ...or it will report what it was, not what we want
          else if (op == "status") { }                                  					// dont report Op, report the real currentState
      state.talk2me = state.talk2me + "The ${noun} is ${arg}.  "      						// talk2me : switch is on (or off) 
}

////////////////////////////////////////////////

def onMethod(){
	onSwitches()?.on()
	def result ="I have turned the light on"
	sendOutput(result)
}
def offMethod(){
	offSwitches()?.off()
	def result ="I have turned the light off"
	sendOutput(result)
}
