---
layout: youtubepost
title:  "Dagger 2 - A New Type of Dependency Injection"
date:   2016-01-05 00:00:00
categories: [skims, libraries]
---

{% include page/youtube.html %}
<script>
function onYouTubeIframeAPIReady() {
  player = new YT.Player('video_container', {
    height: '390',
    width: '640',
    videoId: 'oK_XtfXPkqw'
  });
}
</script>

[Gregory Kick](https://twitter.com/gk5885) gives us a quick run-down of Java dependency injection solutions and how Dagger 2 overcomes their shortcomings.

##A Quick Overview of Dependency Injection [[01:49]](javascript:jumpTo(109);)

Greg uses a familiar example, the coffee maker, to illustrate the concept of dependency injection. He will use this throughout the presentation and update the implementation as he introduces each DI library.

##The Problem with Dependency Injection [[04:34]](javascript:jumpTo(274);)

Dependency injection requires the programmer to write thousands of lines of code. The challenge is how to get the benefits while avoiding the boilerplate.

##Solution #1 - Spring [[05:59]](javascript:jumpTo(359);)

He talks about Spring and how it basically does the job. A number of issues were pointed out such as verbosity because of its use of XML, runtime validations, untraceable app flow and the map-like API.

##Solution #2 - Guice [[09:14]](javascript:jumpTo(554);)

Guice eliminated the use of XML and replaced it with annotations and introduced dynamism. However, reflection is still around, app flow is still not traceable, and the map-like API still exists.

##Solution #3 - Dagger 1 [[18:05]](javascript:jumpTo(1085);)

The guys at Square produced Dagger 1 to take on the problem. They reduced reflection, moved configuration processing to compile-time and improved traceability by allowing the programmer to peek at generated code.

##Solution #4 - Dagger 2 [[24:21]](javascript:jumpTo(1461);)

Knowing the success of Dagger 1, Dagger 2 kept its predecessors API. Greg walks us through the most important parts of Dagger 2, providers and components, while explaining how reflection was eliminated, how debugging was made better and how the map-like API was dumped.

##The Status of Dagger 2 [[38:15]](javascript:jumpTo(2295);)

Greg gives the current status of Dagger 2 and expects the support from the community.
