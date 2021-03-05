---
description: >-
  Skills are like apps for a voice assistant. They give Mycroft access to new
  information and new abilities.
---

# Installing New Skills

{% hint style="info" %}
The [Skills Marketplace](https://market.mycroft.ai/skills) is a curated list of Voice Skills that have been tested and approved by the Mycroft Community.
{% endhint %}

## Voice Installation

The simplest way to install a new Skill is to ask Mycroft to do it for you by saying:

> Hey Mycroft, install {skill name}

This will search the Skills Marketplace and install them on your device.

## Removing a Skill

To remove the Skill, you can say:

> Hey Mycroft, uninstall {skill name}

## Command Line

If you prefer to use the command line, you can directly use the [Mycroft Skills Manager](../skill-development/mycroft-skills-manager/).

To install a Skill run:

```text
mycroft-msm install skill-name
```

To remove a Skill run:

```text
mycroft-msm remove skill-name
```

For more detail on command line usage see:

{% page-ref page="../skill-development/mycroft-skills-manager/" %}

## Troubleshooting

If you are having trouble using the Mycroft Skills Manager, or the `mycroft-msm` command cannot be found, see our dedicated troubleshooting guide:

{% page-ref page="../skill-development/mycroft-skills-manager/msm-troubleshooting.md" %}

