# The Telstar Viewdata System

Please note this wiki relates to __TELSTAR 2.0__ only. Telstar 1.0 is no longer under development. For the main
differences between this version and previous versions see [Version Differences](VersionDifferences.md).

If you have arrived here but simply want to download the __Telstar Viewdata Client__, it can be downloaded
from [here](https://bitbucket.org/johnnewcombe/telstar-2/downloads/). Full details of the client and Telstar Videotext
Service can be found here [The Telstar Viewdata System](https://glasstty.com/telstar).

<!--![frames_in_grid_700.png](https://bitbucket.org/repo/aK55EE6/images/783535623-frames_in_grid_700.png)-->
![Pages_wide.png](https://raw.githubusercontent.com/wiki/johnnewcombe/telstar-2/files/pages_wide.png)

## Introduction

TELSTAR is both a viewdata system and a viewdata service. The articles here relate mainly to installing and managing a
standalone Telstar system. Details of the current viewdata service can be found
at [https://glasstty.com](https://glasstty.com).

The TELSTAR viewdata system, provides a simple viewdata/videotex platform similar to those that were prevalent during
the 1980s such as Prestel (https://en.wikipedia.org/wiki/Prestel).

The viewdata implementation described here can be accessed over the internet using modern _internet modems_ in
conjunction with historic viewdata and videotex terminals and home computers of the 1980s. In addition, the Telstar
Viewdata Client can be used with modern computers (macOS, Linux, Windows).

TELSTAR is designed to run in a Docker environment and is supplied as a Docker image (
see https://hub.docker.com/repository/docker/johnnewcombe/telstar). The system can be installed with a few simple Docker
commands or by using Docker Compose. Telstar can also be run under a Kubernetes environment.

Content for the system is based on simple json files, these are added to the system and updated using a command line
utility _telstar-util_ in conjunction with the Telstar API. Telstar includes support for the popular frame editor
available at https://edit.tf.

The Docker image described here is a full implementation of TELSTAR application, and whilst the system is comprehensive,
it is fairly simple to set up. In addition the system can be extended using a very simple plugin architecture.

For the main differences between this version and previous versions see [Version Differences](VersionDifferences.md).

<!--

## Wiki Contents

* [Introduction](Home.md)
* [Installing Telstar](Installing Telstar.md)
* [Routing](routing.md)
* [Frames](frames.md)
* [Configuration Options](configuration_options.md)
* [Managing Telstar with Portainer](portainer.md)
* [Orchestrating Telstar with Docker Compose](docker_compose.md)
* [Orchestrating Telstar with Kubernetes](kubernetes.md)
* [TelstarAPI](telstar_api.md)
* [Plugins](telstar_plugins.md) -->

## Installation

Telstar is implemented using Docker containers. Details of how to install Telstar are detailed in the
article [Installing Telstar](InstallingTelstar.md). Telstar can be configured and customised as required,
see [Configuration Options](ConfigurationOptions.md) for details.

Once the system is installed and running it can be managed using Portainer,
see [Managing Telstar with Portainer](ManagingTelstarWithPortainer.md).

![portainer.png](https://bitbucket.org/repo/LXaBk6A/images/2827664245-portainer.png)

## Pages and Frames

As is typical with interactive viewdata systems, pages are numbered from 0 to 999999999 and consist of one or more
frames with the suffix a-z, for example the first frame for page 200 would be 200a, the second 200b and so on (
see [Routing](Routing.md) for more details). These frames are stored within Telstar's database as JSON objects (
see https://www.json.org/) and uploaded to Telstar using a simple command line utility.

The Telstar utility program simply interacts with the Telstar API which is a restful API. This can be used directly by
any software making it simple to programmatically manage pages and routing information. Further details of the Telstar
API and the command line utility can be found in the section [The Telstar API](TheTelstarAPI.md).

The simplest frame that can be viewed on TELSTAR would be defined as follows. This would create a simple information
frame with some flashing content.

    {
      "pid": {
        "page-no": 101,
        "frame-id": "a"
      },
      "visible": true,
      "content": {
        "data": "[D]This is page[F]101a",
         "type": "markup"
      }
    }

In this example [Markup](Markup.md) has been used within the content, however, other content types can be used including
edi.tf urls. See the section [Frames](Frames.md) for details.

There are several frame types that can be defined including basic information frames, response frames that can capture
user entered data and gateway frames that allow full duplex connections to other systems.
The [The Telstar API](TheTelstarAPI.md) can be used to manage the frames either using the command line
utility (https://glasstty.com/wp-content/uploads/2022/04/telstar-util-2.0.zip) or via custom software as required.

Full details of all of the frame types and how to create them is described in the section [Frames](Frames.md).

## Response Frames and Plugins

Response frames are frames that allow a user to enter data to be processed i.e. a frame where a user enters data such as
a form. The data from these frames would typically be processed using an external program or script referred to as a _
plugin_. The plugin can be any software that can be executed e.g. a binary program or a scripts such as Python or Bash
etc. All that is required is that the plugin returns a json result, this would typically be a frame or collection of
frames.

The Telstar system https://glasstty.com weather page is handled using a response page and plugin. The user is presented
with a response frame asking for a town or city. This is passed to an external plugin written in Go (Golang) which
accesses the Openweather API and returns the current weather and forcast as a collection of JSON frames. These are
captured by Telstar and presented to the user.

Plugins are simple to create and deploy within a Telstar container and can be written in any language supported by the
platform.

Further details of the Telstar Plugin framework and how to create them can be found in the
section [Implementing Response Frames](ImplementingResponseFrames.md).

## Navigation and Routing

Routing in Telstar is handled by an asynchronous process that allows for routing to occur whilst pages are being
rendered. This means that a user of the system can navigate quickly to a desired page by simply entering the page number
one character after another. Each successive key press will start the rendering of a page but as each subsequent key is
pressed, the rendering is cancelled and the following page is rendered and so on until the desired page is rendered.

For example simply pressing _2 2 2_ from the main index page (Page 0a) of the Telstar service will result in the
Guardians UK News being displayed. For this to work correctly, all intermediary pages must be present. Creating routes
through the system such as the one described above, should be the aim when designing a viewdata system.

Users can jump directly to a known page by entering the page number using the format _*page#_, for example, entering _*
92#_ into the current Telstar system will direct you to page 92.

Full details of page numbering, zero-page routing and how to modify routing tables can be found in the
section [Routing](Routing.md).

## Acknowledgements and Attributions

The website icon is derived from work by Dan Farrimond. This original work and the icon is licensed under the Creative
Commons Attribution-Share Alike 3.0 Unported license (https://creativecommons.org/licenses/by-sa/3.0/deed.en).

