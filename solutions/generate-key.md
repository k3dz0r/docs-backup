---
id: "cli-solutions-generate-key"
title: "solutions generate-key"
slug: "/commands/solutions/generate-key"
sidebar_label: "generate-key"
---

Generates a signing key necessary to prepare a <a id="solution"><span className="dashed-underline">solution</span></a>.

Docker needs this key to [pack a solution](/cli/commands/solutions/prepare) into a container.

## Syntax

```
./spctl solutions generate-key <outputPath>
    [--config <CONFIG_PATH>]
    [--help | -h]
```

## Arguments

| **Name** | **Description** |
| :- | :- |
| `<outputPath>` | Path to save the generated key file. |

## Option

| **Name** | **Description** |
| :- | :- |
| `--config <CONFIG_PATH>` | Path to the SPCTL configuration file. The default is `./config.json`. |
| `--help`, `-h` | Help for the command. |

## Example

```
./spctl solutions generate-key ./new-key
```