# CampaignMemberStatus-Script

String TYPE = 'LR ABM';
String DEFAULTT = 'Member';
String RESPONDED = 'In Pipeline';

Set<String> s1 = new Set<String>{'Member','Not Active','Prospecting','Engaged','In Pipeline'};


List<campaignmemberStatus> cmsd = new List<campaignmemberStatus>();
List<campaignmemberStatus> cmsu = new List<campaignmemberStatus>();
List<Campaign> cm =  [select id, type from Campaign where Type =: TYPE];
for(campaignmemberStatus cs: [select id,Label, SortOrder, HasResponded, CampaignId, isDefault from campaignmemberStatus where CampaignId IN: cm]){
	if(s1.contains(cs.Label)){
	    boolean addu = false;
		if(cs.Label == DEFAULTT){
			cs.isDefault = true;
			addu = true;
		}		
		if(cs.Label == RESPONDED){
			addu = true;
			cs.HasResponded = true;
		}
		if(addu){
			cmsu.add(cs);
			system.debug(cs.Label+'_update_'+cs.CampaignId);
		}
	}else{
	    system.debug(cs.Label+'_delete_'+cs.CampaignId);
		cmsd.add(cs);
	}
}

if(cmsu.isEmpty() == false){
 update cmsu;
}
