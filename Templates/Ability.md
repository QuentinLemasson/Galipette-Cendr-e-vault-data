<%*
	let title = tp.file.title
	title = await tp.system.prompt("Compétence : ");
	
	// Find the note titles to dynamically generate the domain list
	const dv = this.app.plugins.plugins["dataview"].api;
	const query = `TABLE titre FROM "Remote-Wiki/Domaines_Techniques" SORT file.name ASC`;
	const result = await dv.queryMarkdown(query);
	const domainList = result.value.split("|").filter((_,index) => index-6 >=0 && (index-6)%4===2).map(d => d.split("]]")[0])

	// prompt for multiple domains
	let userInput = undefined;
	let selectedDomains = [];
	while (userInput!==null && userInput!=="Valider") {
		const suggestions = [...domainList.filter(d => !selectedDomains.some(s => s===d))]
		userInput = await tp.system.suggester([...suggestions, "Valider"], [...suggestions, null], false, "Domaines : " + selectedDomains.join(', '))
		
		if(userInput!==null) {
			selectedDomains.push(userInput)
		}
	}
	tR += "---"
%>
nom:  <% title %>
domaine : [ <% selectedDomains.map(d => "\"[["+d+"]]\"").join(", ") %> ]
créé: <% tp.date.now("dddd Do MMMM YYYY HH:mm") %>
modifié: <% tp.file.last_modified_date("dddd Do MMMM YYYY HH:mm") %>

---
<% await tp.file.move("/Remote-Wiki/Compétences/" + title) %>
# <% title %>