# issue-spider
This is for test and is for collecting the pull requests reopened in rails project, symfony project and bitcoin project

import urllib2
import json
import httplib
import sys
def spider(owner,repo):
    try:
        page=1
        nb_pr_reopened=0 #number of pull requests reopened
        nb_pr_closed=0
        nb_pr_merged=0
        while True:
            pr_url=('https://api.github.com/repos/'+ owner +'/'+ repo + '/pulls?per_page=99&state=all&page='+str(page))
            headers={
                    'User-Agent':'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:36.0) Gecko/20100101 Firefox/36.0',
                     'Authorization':'token 516ed78e0521c6b25d9726ad51fa63841d019936'
                     }
            pr_req = urllib2.Request(pr_url,headers=headers)
            json_obj=urllib2.urlopen(pr_req)
            pr_data=json.load(json_obj)
            #print 'aaaaa'
            for PR_item in pr_data:
                #if PR_item['state']=='open':
                # Getting the link relation of the API location of this Pull Request's issue
                url_ussue= PR_item['issue_url']
                issue_url_req=urllib2.Request(url_ussue,headers=headers)
                json_url_issue=urllib2.urlopen(issue_url_req)
                issue_data=json.load(json_url_issue)

                #Getting the link link of the API location of this issue's events
                url_event=issue_data['events_url']
                event_url_req=urllib2.Request(url_event,headers=headers)
                json_event=urllib2.urlopen(event_url_req)
                events_data=json.load(json_event)

                # Here we are looking for the event status
                for event_item in events_data:
                    #Here we compute the number reopened event of the Pull Requests by test if the event is equal to reopened
                    if event_item ['event']=='reopened':
                        nb_pr_reopened+=1
                        print event_item['url'] # display the url event that contains the reopened pull requests
                        print url_ussue # display the url issue that contains the reopened pull requests
                print 'Page #: ', page #Here we display whole pages which are in this project
                page+=1
    except httplib.BadStatusLine:
        pass
    print('Number of PR reopened is :%s'%(nb_pr_reopened))
if __name__=='__main__':
    spider('bitcoin','bitcoin')
