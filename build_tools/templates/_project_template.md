---
title: {{ name }}, built with MPF
---

<!-- This file is used as the template for all the individual project pages. -->

# {{ name }}

*{{ name }} is an example of a project that runs MPF. [Add yours!](_add_yours.md)*

!!! note "This page was autogenerated. (And temporary!)"

    For a few years, people have been contributing information about their pinball
    projects built using MPF. All of that information is in the
    [`/showcase`](https://github.com/missionpinball/mpf-docs/tree/main/docs/showcase)
    folder in the root of the repo.

    You can still [add your own project](_add_yours.md).

    Since we just did the website migration, we still need to make these project
    pages look nice. We'll probably get to it soon, or, you can help us out and
    submit a PR to make this page look better. :) Edit the
    `/build_tools/templates/_project_template.md` file to change the layout of this page.

**Team**: {{ team }}

**Location**: {{ location }}

**Started**: {{ started }}

**Finished**: {{ finished }}

**Images**: {{ images }}

**Project type**: {{ project_type }}

{% if documentation_link %}
**Documentation Link**: [Here]({{ documentation_link }})
{% endif %}
{% if code_link %}
**Code Link**: [Here]({{ code_link }})
{% endif %}
{% if gameplay_link %}
**Gameplay Link**: [Here]({{ gameplay_link }})
{% endif %}

**Controller**: {{ controller }}

**Description**:

{{ description }}

{% if youtube_video_ids %}

## Video of {{ name }}

<div class="video-wrapper">
<iframe width="560" height="315" src="https://www.youtube.com/embed/{{ youtube_video_ids }}" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>
{% endif %}

<!-- Note, do not edit this file directly, as it will be overwritten when the list is regenerated.

To edit information about a project, edit the project's YAML file in the `/showcase` folder. (Off the
root of the repo, not this folder which is `/www/showcase`.)

To edit the look and feel or layout of this page, edit the `_project_template.md` file in the `/www/showcase` folder. -->