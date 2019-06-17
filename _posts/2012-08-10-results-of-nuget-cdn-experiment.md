---
ID: 54
post_title: Results of NuGet CDN Experiment
author: Howard Dierking
post_excerpt: ""
layout: post
permalink: >
  http://devblogs.microsoft.com/nuget/results-of-nuget-cdn-experiment/
published: true
post_date: 2012-08-10 00:00:00
---
A little over a week ago, [we asked for your help with an experimentt][1] that we were conducting to see whether there was value in enabling CDN support for our packages blob storage container. Over 100 of you responded from all over the world, and the data that you provided made it pretty clear that there was sufficient justification for turning on CDN support for the production NuGet blob storage container. So firstly, thank you to everyone who participated in the experiment!

In addition to turning on CDN, we wanted to share the results of the data that you provided us. First, the worldwide numbers look like the following:

<table>
  <tr>
    <td>
       
    </td>
    
    <td>
      No-CDN
    </td>
    
    <td>
      CDN
    </td>
  </tr>
  
  <tr>
    <td>
      Mean
    </td>
    
    <td>
      33.8252
    </td>
    
    <td>
      22.88007
    </td>
  </tr>
  
  <tr>
    <td>
      Variance
    </td>
    
    <td>
      601.3293
    </td>
    
    <td>
      440.8645
    </td>
  </tr>
  
  <tr>
    <td>
      Observations
    </td>
    
    <td>
      108
    </td>
    
    <td>
      108
    </td>
  </tr>
  
  <tr>
    <td>
      Pearson Correlation
    </td>
    
    <td>
      0.704394
    </td>
    
    <td>
       
    </td>
  </tr>
  
  <tr>
    <td>
      Hypothesized Mean Difference
    </td>
    
    <td>
      6.8
    </td>
    
    <td>
       
    </td>
  </tr>
  
  <tr>
    <td>
      df
    </td>
    
    <td>
      107
    </td>
    
    <td>
       
    </td>
  </tr>
  
  <tr>
    <td>
      t Stat
    </td>
    
    <td>
      2.420113
    </td>
    
    <td>
       
    </td>
  </tr>
  
  <tr>
    <td>
      P(T<=t) one-tail
    </td>
    
    <td>
      0.0086
    </td>
    
    <td>
       
    </td>
  </tr>
  
  <tr>
    <td>
      t Critical one-tail
    </td>
    
    <td>
      1.659219
    </td>
    
    <td>
       
    </td>
  </tr>
  
  <tr>
    <td>
      P(T<=t) two-tail
    </td>
    
    <td>
      0.0172
    </td>
    
    <td>
       
    </td>
  </tr>
  
  <tr>
    <td>
      t Critical two-tail
    </td>
    
    <td>
      1.982383
    </td>
    
    <td>
       
    </td>
  </tr>
</table>

As you can see, there are some additional tests that we included so that you statisticians out there can get a deeper sense of our methodology, but at a high level, you can see that the worldwide average time to download the top 25 NuGet packages *improved by over 10 seconds!*

The improvements are even more pronounced depending on the specific region being considered. In the chart below, each vertical bar represents the range of values for the percentage of improvement and the horizontal line dividing each bar represents the average improvement. As you can see, the greatest improvements were seen in Australia with an average improvement of over 40%. Additionally, notable improvements can also be seen in each of the other regions from which we collected data.

![CDN improvements by region][2]

You can anecdotally draw further conclusions to explain the latency numbers by looking at the landscape of Azure CDN nodes as it exists today.

![Azure CDN ][3]

All in all, the experiment was a great success, and we will be activating CDN in production very soon. We couldn't have collected this level of data without your help, so again, many thanks!

 [1]: http://blog.nuget.org/20120802/help-us-test-nuget-cdn.html
 [2]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/cdn-improvement.png
 [3]: https://devblogs.microsoft.com/nuget/wp-content/uploads/sites/49/2019/05/950.gif