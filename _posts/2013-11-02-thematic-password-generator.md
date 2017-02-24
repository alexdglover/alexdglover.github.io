---
title: Thematic Password Generator
date: 2013-11-02 12:20:32.000000000 -05:00
categories:
- Utilities And Other Useful Things
tags:
- password
- password generator
---
<p>I've been wanting to build a thematic password generator for some time. Basically, choose some sort of "theme" and a few password strength parameters, and it will generate random passwords for you. Will come back to update this post and expand on themes shortly.</p>

<p>Pick a theme:</p>
<select id="themeSelector" onchange="populateDictionaryTextArea()">
  <option disabled="disabled" selected="selected">Choose a theme...</option>
  <option value="flowers">Flowers</option>
  <option value="starwars">Star Wars</option>
  <option value="seaAnimals">Sea Animals</option>
  <option value="jellyBellyFlavors">Jelly Belly Flavors</option>
  <option value="countries">Countries</option>
  <option value="phonetic">Phonetic (military) Alphabet</option>
</select>
<p>Words that will be used for this password (editable):</p>
<p><textarea id="dictionaryTextArea" cols="50" rows="5"></textarea></p>
<p>Minimum password length:</p>
<input id="passwordLength" type="text" /><br />

<div>
<p class="align-left" style="padding-right:10px;">Embed numbers?</p>
<p class="checkboxThree align-left">
  <input type="checkbox" id="embedNumbersChkBox" />
  <label for="embedNumbersChkBox"></label>
</p>
</div>

<div style="clear:both;"></div>

<div>
<p class="align-left" style="padding-right:10px;">Capitalize first letters?</p>
<p class="checkboxThree align-left">
  <input type="checkbox" id="capitalizeChkBox" />
  <label for="capitalizeChkBox"></label>
</p>
</div>

<div style="clear:both;"></div>

<div>
<p class="align-left" style="padding-right:10px;">Use exact length?</p>
<p class="checkboxThree align-left">
  <input type="checkbox" id="exactLengthChkBox" />
  <label for="exactLengthChkBox"></label>
</p>
</div>

<div style="clear:both;"></div>

<p><input type="button" onclick="makePasswd()" class="btn btn--large btn--success" value="Generate" /></p>

<p><textarea id="output" cols="50" disabled="disabled" rows="5">Password will appear here...</textarea></p>

<script>
function makePasswd() {
    var password = '';
    var desiredLength = document.getElementById('passwordLength').value;
    var embedNumbers = document.getElementById('embedNumbersChkBox').checked;
    var capitalize = document.getElementById('capitalizeChkBox').checked;
    var exactLength = document.getElementById('exactLengthChkBox').checked;

  var words = document.getElementById('dictionaryTextArea').value.split(" ");
    while (password.length < desiredLength) {
        var word = words[Math.floor(Math.random()*words.length)];
        if(capitalize){
            word = word.charAt(0).toUpperCase() + word.slice(1);
        }
        password += word;
        if(embedNumbers && (password.length < desiredLength)){
            password += Math.floor(Math.random()*51);
        }
    }
    if(exactLength){
        password = password.slice(0,desiredLength);
    }
    var actualLength = password.length;
    password += "\n\nPasswword is " + actualLength + " characters long.";

  document.getElementById('output').value = password;
}

function populateDictionaryTextArea(){
  var dictionaries = { samuelLJackson : ["bullshit","shoot","bitch","kneecaps","goddamn","money","motherfucker","Winston","motherfucker","shot","understand","asses","dead","fucking","fried","chicken","shit","transitional","kill","shit","case","dumbass","duty","booty","motherfucker","righteous","ezekiel","brother","tyranny","filthy","pumpkin-pie","AK-47","kill","absolutely","snakes","plane","zeus","fuck","shove","lightning-bolt","ass","goddamn","shark","enough"], pokemon: ["Bulbasaur","Ivysaur","Venusaur","Charmander","Charmeleon","Charizard","Squirtle","Wartortle","Blastoise","Caterpie","Metapod","Butterfree","Weedle","Kakuna","Beedrill","Pidgey","Pidgeotto","Pidgeot","Rattata","Raticate","Spearow","Fearow","Ekans","Arbok","Pikachu","Raichu","Sandshrew","Sandslash","Nidoran","Nidorina","Nidoqueen","Nidoran","Nidorino","Nidoking","Clefairy","Clefable","Vulpix","Ninetales","Jigglypuff","Wigglytuff","Zubat","Golbat","Oddish","Gloom","Vileplume","Paras","Parasect","Venonat","Venomoth","Diglett","Dugtrio","Meowth","Persian","Psyduck","Golduck","Mankey","Primeape","Growlithe","Arcanine","Poliwag","Poliwhirl","Poliwrath","Abra","Kadabra","Alakazam","Machop","Machoke","Machamp","Bellsprout","Weepinbell","Victreebel","Tentacool","Tentacruel","Geodude","Graveler","Golem","Ponyta","Rapidash","Slowpoke","Slowbro","Magnemite","Magneton","Farfetchd","Doduo","Dodrio","Seel","Dewgong","Grimer","Muk","Shellder","Cloyster","Gastly","Haunter","Gengar","Onix","Drowzee","Hypno","Krabby","Kingler","Voltorb","Electrode","Exeggcute","Exeggutor","Cubone","Marowak","Hitmonlee","Hitmonchan","Lickitung","Koffing","Weezing","Rhyhorn","Rhydon","Chansey","Tangela","Kangaskhan","Horsea","Seadra","Goldeen","Seaking","Staryu","Starmie","Mr. Mime","Scyther","Jynx","Electabuzz","Magmar","Pinsir","Tauros","Magikarp","Gyarados","Lapras","Ditto","Eevee","Vaporeon","Jolteon","Flareon","Porygon","Omanyte","Omastar","Kabuto","Kabutops","Aerodactyl","Snorlax","Articuno","Zapdos","Moltres","Dratini","Dragonair","Dragonite","Mewtwo","Mew†","Chikorita","Bayleef","Meganium","Cyndaquil","Quilava","Typhlosion","Totodile","Croconaw","Feraligatr","Sentret","Furret","Hoothoot","Noctowl","Ledyba","Ledian","Spinarak","Ariados","Crobat","Chinchou","Lanturn","Pichu","Cleffa","Igglybuff","Togepi","Togetic","Natu","Xatu","Mareep","Flaaffy","Ampharos","Bellossom","Marill","Azumarill","Sudowoodo","Politoed","Hoppip","Skiploom","Jumpluff","Aipom","Sunkern","Sunflora","Yanma","Wooper","Quagsire","Espeon","Umbreon","Murkrow","Slowking","Misdreavus","Unown","Wobbuffet","Girafarig","Pineco","Forretress","Dunsparce","Gligar","Steelix","Snubbull","Granbull","Qwilfish","Scizor","Shuckle","Heracross","Sneasel","Teddiursa","Ursaring","Slugma","Magcargo","Swinub","Piloswine","Corsola","Remoraid","Octillery","Delibird","Mantine","Skarmory","Houndour","Houndoom","Kingdra","Phanpy","Donphan","Porygon2","Stantler","Smeargle","Tyrogue","Hitmontop","Smoochum","Elekid","Magby","Miltank","Blissey","Raikou","Entei","Suicune","Larvitar","Pupitar","Tyranitar","Lugia†","Ho-Oh†","Celebi†","Treecko","Grovyle","Sceptile","Torchic","Combusken","Blaziken","Mudkip","Marshtomp","Swampert","Poochyena","Mightyena","Zigzagoon","Linoone","Wurmple","Silcoon","Beautifly","Cascoon","Dustox","Lotad","Lombre","Ludicolo","Seedot","Nuzleaf","Shiftry","Taillow","Swellow","Wingull","Pelipper","Ralts","Kirlia","Gardevoir","Surskit","Masquerain","Shroomish","Breloom","Slakoth","Vigoroth","Slaking","Nincada","Ninjask","Shedinja","Whismur","Loudred","Exploud","Makuhita","Hariyama","Azurill","Nosepass","Skitty","Delcatty","Sableye","Mawile","Aron","Lairon","Aggron","Meditite","Medicham","Electrike","Manectric","Plusle","Minun","Volbeat","Illumise","Roselia","Gulpin","Swalot","Carvanha","Sharpedo","Wailmer","Wailord","Numel","Camerupt","Torkoal","Spoink","Grumpig","Spinda","Trapinch","Vibrava","Flygon","Cacnea","Cacturne","Swablu","Altaria","Zangoose","Seviper","Lunatone","Solrock","Barboach","Whiscash","Corphish","Crawdaunt","Baltoy","Claydol","Lileep","Cradily","Anorith","Armaldo","Feebas","Milotic","Castform","Kecleon","Shuppet","Banette","Duskull","Dusclops","Tropius","Chimecho","Absol","Wynaut","Snorunt","Glalie","Spheal","Sealeo","Walrein","Clamperl","Huntail","Gorebyss","Relicanth","Luvdisc","Bagon","Shelgon","Salamence","Beldum","Metang","Metagross","Regirock","Regice","Registeel","Latias","Latios","Kyogre","Groudon","Rayquaza","Jirachi†","Deoxys†","Turtwig","Grotle","Torterra","Chimchar","Monferno","Infernape","Piplup","Prinplup","Empoleon","Starly","Staravia","Staraptor","Bidoof","Bibarel","Kricketot","Kricketune","Shinx","Luxio","Luxray","Budew","Roserade","Cranidos","Rampardos","Shieldon","Bastiodon","Burmy","Wormadam","Mothim","Combee","Vespiquen","Pachirisu","Buizel","Floatzel","Cherubi","Cherrim","Shellos","Gastrodon","Ambipom","Drifloon","Drifblim","Buneary","Lopunny","Mismagius","Honchkrow","Glameow","Purugly","Chingling","Stunky","Skuntank","Bronzor","Bronzong","Bonsly","Mime Jr.","Happiny","Chatot","Spiritomb","Gible","Gabite","Garchomp","Munchlax","Riolu","Lucario","Hippopotas","Hippowdon","Skorupi","Drapion","Croagunk","Toxicroak","Carnivine","Finneon","Lumineon","Mantyke","Snover","Abomasnow","Weavile","Magnezone","Lickilicky","Rhyperior","Tangrowth","Electivire","Magmortar","Togekiss","Yanmega","Leafeon","Glaceon","Gliscor","Mamoswine","Porygon-Z","Gallade","Probopass","Dusknoir","Froslass","Rotom","Uxie","Mesprit","Azelf","Dialga","Palkia","Heatran","Regigigas","Giratina","Cresselia","Swoobat"], flowers: ["alstroemeria","amaranthus","amaryllis","anemone","anthurium","asters","buplerum","callalilies","callalily","carnation","mum","chrysanthemum","coxcomb","daisy","curly-willow","daffodils","dahlias","delphinium","gerbera","ginger","gladiouli","heather","heliconia","hyacinth","hydrangeas","hypericum","iris","kangaroo-paw","larkspur","leptospermum","liatris","lilies","limonium","lisianthus","asters","narcissus","orchids","pearl-blossom","peonies","poinsettia","protea","quince","ranunculus","roses","snapdragons","soldaster","sunflowers","tulips","viburnum","waxflower"], starwars: ["tan-tan","luke","han","skywalker","solo","bacta","at-at","at-st","wookie","wampa","hoth","empire","dark","side","force","leia","princess","speeder","tie-fighter","deathstar","feeling","lightsaber","x-wing","b-wing","y-wing","a-wing","millenium","falcon","destroyer","star","imperial","sith","jedi","knight","tie-bomber","blaster","shield","shields","endor","coruscant","clones","deathsticks","kaashyyk","bothans","proton","torpedo","attack","formation","spacestation","chewbacca","c3po","r2d2","droid","anakin","obi-wan","kenobi","maul","darth","vader","palpatine","emperor","operational","alderaan"], seaAnimals: ["anemone","eel","carp","pike","walleye","clownfish","kraken","barracuda","shark","angelfish","squid","octopus","crab","lobster","urchin","whale","dolphin","seasnake","manatee","cuttlefish","conch","cod","salmon","tuna","barnacle","anchovy","albacore","coral","seal","flounder","goldfish","grouper","guppy","haddock","halibut","herring","jellyfish","lamprey","manta-ray","stingray","minnow","mollusk","mussel","narwhal","nautilus","nettlefish","orca","otter","oyster","plankton","piranha","sardine","shrimp","starfish","sturgeon","trout","turtle","walrus"], jellyBellyFlavors: ["cream","soda","root","beer","berry","blue","blueberry","bubble","gum","buttered","popcorn","cantaloupe","cappuccino","caramel","corn","chili","mango","chocolate","pudding","cinnamon","coconut","cotton","candy","crushed","pineapple","dr.pepper","french","vanilla","green","apple","island","punch","juicy","pear","kiwi","lemon","drop","lemon","lime","licorice","mango","margarita","mixed","berry","smoothie","orange","sherbet","peach","piña","colada","plum","pomegranate","raspberry","red","apple","sizzling","cinnamon","sour","cherry","strawberry","cheesecake","strawberry","daiquiri","strawberry","jam","sunkist","lemon","sunkist","lime","sunkist","orange","sunkist","pink","grapefruit","sunkist","tangerine","toasted","marshmallow","top","banana","tutti-fruitti","very","cherry","watermelon","wild","blackberry"], countries:["afghanistan","albania","algeria","american-samoa","andorra","angola","anguilla","antigua","barbuda","argentina","armenia","aruba","australia","austria","azerbaijan","bahamas","bahrain","bangladesh","barbados","belarus","belgium","belize","benin","bermuda","bhutan","bolivia","bosnia-herzegovina","botswana","bouvet-island","brazil","brunei","bulgaria","burkina-faso","burundi","cambodia","cameroon","canada","cape-verde","cayman-islands","central-african-republic","chad","chile","china","christmas-island","cocos-(keeling)-islands","colombia","comoros","zaire","cook-islands","costa-rica","croatia","cuba","cyprus","czech-republic","denmark","djibouti","dominica","dominican-republic","ecuador","egypt","el-salvador","equatorial-guinea","eritrea","estonia","ethiopia","falkland-islands","faroe-islands","fiji","finland","france","french-guiana","gabon","gambia","georgia","germany","ghana","gibraltar","greece","greenland","grenada","guadeloupe","guam","guatemala","guinea","guinea-bissau","guyana","haiti","holy-see","honduras","hong-kong","hungary","iceland","india","indonesia","iran","iraq","ireland","israel","italy","ivory-coast","jamaica","japan","jordan","kazakhstan","kenya","kiribati","kuwait","kyrgyzstan","laos","latvia","lebanon","lesotho","liberia","libya","liechtenstein","lithuania","luxembourg","macau","macedonia","madagascar","malawi","malaysia","maldives","mali","malta","marshall-islands","martinique","mauritania","mauritius","mayotte","mexico","micronesia","moldova","monaco","mongolia","montenegro","montserrat","morocco","mozambique","myanmar","namibia","nauru","nepal","netherlands","antilles","new-caledonia","new-zealand","nicaragua","niger","nigeria","niue","norfolk-island","north-korea","northern-mariana-islands","norway","oman","pakistan","palau","panama","papua-new-guinea","paraguay","peru","philippines","pitcairn-island","poland","polynesia","portugal","puerto-rico","qatar","reunion","romania","russia","rwanda","saint-helena","saint-kitts-and-nevis","saint-lucia","saint-pierre-and-miquelon","saint-vincent-and-grenadines","samoa","san-marino","sao-tome-and-principe","saudi-arabia","senegal","serbia","seychelles","sierra-leone","singapore","slovakia","slovenia","solomon-islands","somalia","south-africa","south-georgia-and-south-sandwich-islands","south-korea","south-sudan","spain","sri-lanka","sudan","suriname","svalbard-and-jan-mayen-islands","swaziland","sweden","switzerland","syria","taiwan","tajikistan","tanzania","thailand","timor-leste","togo","tokelau","tonga","trinidad-and-tobago","tunisia","turkey","turkmenistan","turks-and-caicos-islands","tuvalu","uganda","ukraine","united-arab-emirates","united-kingdom","united-states","uruguay","uzbekistan","vanuatu","venezuela","vietnam","virgin-islands","wallis-and-futuna-islands","yemen","zambia","zimbabwe"], phonetic : ["alpha","bravo","charlie","delta","echo","foxtrot","gulf","hotel","india","juliet","kilo","lima","mike","november","oscar","papa","quebec","romeo","sierra","tango","uniform","victor","whiskey","x-ray","yankee","zulu"] };

  var dictionarySelect   = document.getElementById('themeSelector');
  var dictionaryName    = dictionarySelect.options[dictionarySelect.selectedIndex].value;

  //alert(dictionaryName);

  var dictionaryTextArea  = document.getElementById('dictionaryTextArea');

  var dictionary = dictionaries[dictionaryName];

  dictionaryTextArea.value = "";

  for(var i = 0; i < dictionary.length; i++){
    dictionaryTextArea.value += dictionary[i];
    dictionaryTextArea.value += " ";
  }
}
</script>

Here's the code if you're interested:

```html
<p>Pick a theme:</p>
<select id="themeSelector" onchange="populateDictionaryTextArea()">
  <option disabled="disabled" selected="selected">Choose a theme...</option>
  <option value="flowers">Flowers</option>
  <option value="starwars">Star Wars</option>
  <option value="seaAnimals">Sea Animals</option>
  <option value="jellyBellyFlavors">Jelly Belly Flavors</option>
  <option value="countries">Countries</option>
  <option value="phonetic">Phonetic (military) Alphabet</option>
</select>
<p>Words that will be used for this password (editable):</p>
<p><textarea id="dictionaryTextArea" cols="50" rows="5"></textarea></p>
<p>Minimum password length:</p>
<input id="passwordLength" type="text" /><br />

<div>
<p class="align-left" style="padding-right:10px;">Embed numbers?</p>
<p class="checkboxThree align-left">
  <input type="checkbox" id="embedNumbersChkBox" />
  <label for="embedNumbersChkBox"></label>
</p>
</div>

<div style="clear:both;"></div>

<div>
<p class="align-left" style="padding-right:10px;">Capitalize first letters?</p>
<p class="checkboxThree align-left">
  <input type="checkbox" id="capitalizeChkBox" />
  <label for="capitalizeChkBox"></label>
</p>
</div>

<div style="clear:both;"></div>

<div>
<p class="align-left" style="padding-right:10px;">Use exact length?</p>
<p class="checkboxThree align-left">
  <input type="checkbox" id="exactLengthChkBox" />
  <label for="exactLengthChkBox"></label>
</p>
</div>

<div style="clear:both;"></div>

<p><input type="button" onclick="makePasswd()" class="btn btn--large btn--success" value="Generate" /></p>

<p><textarea id="output" cols="50" disabled="disabled" rows="5">Password will appear here...</textarea></p>

<script>
function makePasswd() {
    var password = '';
    var desiredLength = document.getElementById('passwordLength').value;
    var embedNumbers = document.getElementById('embedNumbersChkBox').checked;
    var capitalize = document.getElementById('capitalizeChkBox').checked;
    var exactLength = document.getElementById('exactLengthChkBox').checked;

  var words = document.getElementById('dictionaryTextArea').value.split(" ");
    while (password.length < desiredLength) {
        var word = words[Math.floor(Math.random()*words.length)];
        if(capitalize){
            word = word.charAt(0).toUpperCase() + word.slice(1);
        }
        password += word;
        if(embedNumbers && (password.length < desiredLength)){
            password += Math.floor(Math.random()*51);
        }
    }
    if(exactLength){
        password = password.slice(0,desiredLength);
    }
    var actualLength = password.length;
    password += "\n\nPasswword is " + actualLength + " characters long.";

  document.getElementById('output').value = password;
}

function populateDictionaryTextArea(){
  var dictionaries = { samuelLJackson : ["bullshit","shoot","bitch","kneecaps","goddamn","money","motherfucker","Winston","motherfucker","shot","understand","asses","dead","fucking","fried","chicken","shit","transitional","kill","shit","case","dumbass","duty","booty","motherfucker","righteous","ezekiel","brother","tyranny","filthy","pumpkin-pie","AK-47","kill","absolutely","snakes","plane","zeus","fuck","shove","lightning-bolt","ass","goddamn","shark","enough"], pokemon: ["Bulbasaur","Ivysaur","Venusaur","Charmander","Charmeleon","Charizard","Squirtle","Wartortle","Blastoise","Caterpie","Metapod","Butterfree","Weedle","Kakuna","Beedrill","Pidgey","Pidgeotto","Pidgeot","Rattata","Raticate","Spearow","Fearow","Ekans","Arbok","Pikachu","Raichu","Sandshrew","Sandslash","Nidoran","Nidorina","Nidoqueen","Nidoran","Nidorino","Nidoking","Clefairy","Clefable","Vulpix","Ninetales","Jigglypuff","Wigglytuff","Zubat","Golbat","Oddish","Gloom","Vileplume","Paras","Parasect","Venonat","Venomoth","Diglett","Dugtrio","Meowth","Persian","Psyduck","Golduck","Mankey","Primeape","Growlithe","Arcanine","Poliwag","Poliwhirl","Poliwrath","Abra","Kadabra","Alakazam","Machop","Machoke","Machamp","Bellsprout","Weepinbell","Victreebel","Tentacool","Tentacruel","Geodude","Graveler","Golem","Ponyta","Rapidash","Slowpoke","Slowbro","Magnemite","Magneton","Farfetchd","Doduo","Dodrio","Seel","Dewgong","Grimer","Muk","Shellder","Cloyster","Gastly","Haunter","Gengar","Onix","Drowzee","Hypno","Krabby","Kingler","Voltorb","Electrode","Exeggcute","Exeggutor","Cubone","Marowak","Hitmonlee","Hitmonchan","Lickitung","Koffing","Weezing","Rhyhorn","Rhydon","Chansey","Tangela","Kangaskhan","Horsea","Seadra","Goldeen","Seaking","Staryu","Starmie","Mr. Mime","Scyther","Jynx","Electabuzz","Magmar","Pinsir","Tauros","Magikarp","Gyarados","Lapras","Ditto","Eevee","Vaporeon","Jolteon","Flareon","Porygon","Omanyte","Omastar","Kabuto","Kabutops","Aerodactyl","Snorlax","Articuno","Zapdos","Moltres","Dratini","Dragonair","Dragonite","Mewtwo","Mew†","Chikorita","Bayleef","Meganium","Cyndaquil","Quilava","Typhlosion","Totodile","Croconaw","Feraligatr","Sentret","Furret","Hoothoot","Noctowl","Ledyba","Ledian","Spinarak","Ariados","Crobat","Chinchou","Lanturn","Pichu","Cleffa","Igglybuff","Togepi","Togetic","Natu","Xatu","Mareep","Flaaffy","Ampharos","Bellossom","Marill","Azumarill","Sudowoodo","Politoed","Hoppip","Skiploom","Jumpluff","Aipom","Sunkern","Sunflora","Yanma","Wooper","Quagsire","Espeon","Umbreon","Murkrow","Slowking","Misdreavus","Unown","Wobbuffet","Girafarig","Pineco","Forretress","Dunsparce","Gligar","Steelix","Snubbull","Granbull","Qwilfish","Scizor","Shuckle","Heracross","Sneasel","Teddiursa","Ursaring","Slugma","Magcargo","Swinub","Piloswine","Corsola","Remoraid","Octillery","Delibird","Mantine","Skarmory","Houndour","Houndoom","Kingdra","Phanpy","Donphan","Porygon2","Stantler","Smeargle","Tyrogue","Hitmontop","Smoochum","Elekid","Magby","Miltank","Blissey","Raikou","Entei","Suicune","Larvitar","Pupitar","Tyranitar","Lugia†","Ho-Oh†","Celebi†","Treecko","Grovyle","Sceptile","Torchic","Combusken","Blaziken","Mudkip","Marshtomp","Swampert","Poochyena","Mightyena","Zigzagoon","Linoone","Wurmple","Silcoon","Beautifly","Cascoon","Dustox","Lotad","Lombre","Ludicolo","Seedot","Nuzleaf","Shiftry","Taillow","Swellow","Wingull","Pelipper","Ralts","Kirlia","Gardevoir","Surskit","Masquerain","Shroomish","Breloom","Slakoth","Vigoroth","Slaking","Nincada","Ninjask","Shedinja","Whismur","Loudred","Exploud","Makuhita","Hariyama","Azurill","Nosepass","Skitty","Delcatty","Sableye","Mawile","Aron","Lairon","Aggron","Meditite","Medicham","Electrike","Manectric","Plusle","Minun","Volbeat","Illumise","Roselia","Gulpin","Swalot","Carvanha","Sharpedo","Wailmer","Wailord","Numel","Camerupt","Torkoal","Spoink","Grumpig","Spinda","Trapinch","Vibrava","Flygon","Cacnea","Cacturne","Swablu","Altaria","Zangoose","Seviper","Lunatone","Solrock","Barboach","Whiscash","Corphish","Crawdaunt","Baltoy","Claydol","Lileep","Cradily","Anorith","Armaldo","Feebas","Milotic","Castform","Kecleon","Shuppet","Banette","Duskull","Dusclops","Tropius","Chimecho","Absol","Wynaut","Snorunt","Glalie","Spheal","Sealeo","Walrein","Clamperl","Huntail","Gorebyss","Relicanth","Luvdisc","Bagon","Shelgon","Salamence","Beldum","Metang","Metagross","Regirock","Regice","Registeel","Latias","Latios","Kyogre","Groudon","Rayquaza","Jirachi†","Deoxys†","Turtwig","Grotle","Torterra","Chimchar","Monferno","Infernape","Piplup","Prinplup","Empoleon","Starly","Staravia","Staraptor","Bidoof","Bibarel","Kricketot","Kricketune","Shinx","Luxio","Luxray","Budew","Roserade","Cranidos","Rampardos","Shieldon","Bastiodon","Burmy","Wormadam","Mothim","Combee","Vespiquen","Pachirisu","Buizel","Floatzel","Cherubi","Cherrim","Shellos","Gastrodon","Ambipom","Drifloon","Drifblim","Buneary","Lopunny","Mismagius","Honchkrow","Glameow","Purugly","Chingling","Stunky","Skuntank","Bronzor","Bronzong","Bonsly","Mime Jr.","Happiny","Chatot","Spiritomb","Gible","Gabite","Garchomp","Munchlax","Riolu","Lucario","Hippopotas","Hippowdon","Skorupi","Drapion","Croagunk","Toxicroak","Carnivine","Finneon","Lumineon","Mantyke","Snover","Abomasnow","Weavile","Magnezone","Lickilicky","Rhyperior","Tangrowth","Electivire","Magmortar","Togekiss","Yanmega","Leafeon","Glaceon","Gliscor","Mamoswine","Porygon-Z","Gallade","Probopass","Dusknoir","Froslass","Rotom","Uxie","Mesprit","Azelf","Dialga","Palkia","Heatran","Regigigas","Giratina","Cresselia","Swoobat"], flowers: ["alstroemeria","amaranthus","amaryllis","anemone","anthurium","asters","buplerum","callalilies","callalily","carnation","mum","chrysanthemum","coxcomb","daisy","curly-willow","daffodils","dahlias","delphinium","gerbera","ginger","gladiouli","heather","heliconia","hyacinth","hydrangeas","hypericum","iris","kangaroo-paw","larkspur","leptospermum","liatris","lilies","limonium","lisianthus","asters","narcissus","orchids","pearl-blossom","peonies","poinsettia","protea","quince","ranunculus","roses","snapdragons","soldaster","sunflowers","tulips","viburnum","waxflower"], starwars: ["tan-tan","luke","han","skywalker","solo","bacta","at-at","at-st","wookie","wampa","hoth","empire","dark","side","force","leia","princess","speeder","tie-fighter","deathstar","feeling","lightsaber","x-wing","b-wing","y-wing","a-wing","millenium","falcon","destroyer","star","imperial","sith","jedi","knight","tie-bomber","blaster","shield","shields","endor","coruscant","clones","deathsticks","kaashyyk","bothans","proton","torpedo","attack","formation","spacestation","chewbacca","c3po","r2d2","droid","anakin","obi-wan","kenobi","maul","darth","vader","palpatine","emperor","operational","alderaan"], seaAnimals: ["anemone","eel","carp","pike","walleye","clownfish","kraken","barracuda","shark","angelfish","squid","octopus","crab","lobster","urchin","whale","dolphin","seasnake","manatee","cuttlefish","conch","cod","salmon","tuna","barnacle","anchovy","albacore","coral","seal","flounder","goldfish","grouper","guppy","haddock","halibut","herring","jellyfish","lamprey","manta-ray","stingray","minnow","mollusk","mussel","narwhal","nautilus","nettlefish","orca","otter","oyster","plankton","piranha","sardine","shrimp","starfish","sturgeon","trout","turtle","walrus"], jellyBellyFlavors: ["cream","soda","root","beer","berry","blue","blueberry","bubble","gum","buttered","popcorn","cantaloupe","cappuccino","caramel","corn","chili","mango","chocolate","pudding","cinnamon","coconut","cotton","candy","crushed","pineapple","dr.pepper","french","vanilla","green","apple","island","punch","juicy","pear","kiwi","lemon","drop","lemon","lime","licorice","mango","margarita","mixed","berry","smoothie","orange","sherbet","peach","piña","colada","plum","pomegranate","raspberry","red","apple","sizzling","cinnamon","sour","cherry","strawberry","cheesecake","strawberry","daiquiri","strawberry","jam","sunkist","lemon","sunkist","lime","sunkist","orange","sunkist","pink","grapefruit","sunkist","tangerine","toasted","marshmallow","top","banana","tutti-fruitti","very","cherry","watermelon","wild","blackberry"], countries:["afghanistan","albania","algeria","american-samoa","andorra","angola","anguilla","antigua","barbuda","argentina","armenia","aruba","australia","austria","azerbaijan","bahamas","bahrain","bangladesh","barbados","belarus","belgium","belize","benin","bermuda","bhutan","bolivia","bosnia-herzegovina","botswana","bouvet-island","brazil","brunei","bulgaria","burkina-faso","burundi","cambodia","cameroon","canada","cape-verde","cayman-islands","central-african-republic","chad","chile","china","christmas-island","cocos-(keeling)-islands","colombia","comoros","zaire","cook-islands","costa-rica","croatia","cuba","cyprus","czech-republic","denmark","djibouti","dominica","dominican-republic","ecuador","egypt","el-salvador","equatorial-guinea","eritrea","estonia","ethiopia","falkland-islands","faroe-islands","fiji","finland","france","french-guiana","gabon","gambia","georgia","germany","ghana","gibraltar","greece","greenland","grenada","guadeloupe","guam","guatemala","guinea","guinea-bissau","guyana","haiti","holy-see","honduras","hong-kong","hungary","iceland","india","indonesia","iran","iraq","ireland","israel","italy","ivory-coast","jamaica","japan","jordan","kazakhstan","kenya","kiribati","kuwait","kyrgyzstan","laos","latvia","lebanon","lesotho","liberia","libya","liechtenstein","lithuania","luxembourg","macau","macedonia","madagascar","malawi","malaysia","maldives","mali","malta","marshall-islands","martinique","mauritania","mauritius","mayotte","mexico","micronesia","moldova","monaco","mongolia","montenegro","montserrat","morocco","mozambique","myanmar","namibia","nauru","nepal","netherlands","antilles","new-caledonia","new-zealand","nicaragua","niger","nigeria","niue","norfolk-island","north-korea","northern-mariana-islands","norway","oman","pakistan","palau","panama","papua-new-guinea","paraguay","peru","philippines","pitcairn-island","poland","polynesia","portugal","puerto-rico","qatar","reunion","romania","russia","rwanda","saint-helena","saint-kitts-and-nevis","saint-lucia","saint-pierre-and-miquelon","saint-vincent-and-grenadines","samoa","san-marino","sao-tome-and-principe","saudi-arabia","senegal","serbia","seychelles","sierra-leone","singapore","slovakia","slovenia","solomon-islands","somalia","south-africa","south-georgia-and-south-sandwich-islands","south-korea","south-sudan","spain","sri-lanka","sudan","suriname","svalbard-and-jan-mayen-islands","swaziland","sweden","switzerland","syria","taiwan","tajikistan","tanzania","thailand","timor-leste","togo","tokelau","tonga","trinidad-and-tobago","tunisia","turkey","turkmenistan","turks-and-caicos-islands","tuvalu","uganda","ukraine","united-arab-emirates","united-kingdom","united-states","uruguay","uzbekistan","vanuatu","venezuela","vietnam","virgin-islands","wallis-and-futuna-islands","yemen","zambia","zimbabwe"], phonetic : ["alpha","bravo","charlie","delta","echo","foxtrot","gulf","hotel","india","juliet","kilo","lima","mike","november","oscar","papa","quebec","romeo","sierra","tango","uniform","victor","whiskey","x-ray","yankee","zulu"] };

  var dictionarySelect   = document.getElementById('themeSelector');
  var dictionaryName    = dictionarySelect.options[dictionarySelect.selectedIndex].value;

  //alert(dictionaryName);

  var dictionaryTextArea  = document.getElementById('dictionaryTextArea');

  var dictionary = dictionaries[dictionaryName];

  dictionaryTextArea.value = "";

  for(var i = 0; i < dictionary.length; i++){
    dictionaryTextArea.value += dictionary[i];
    dictionaryTextArea.value += " ";
  }
}
</script>
```
