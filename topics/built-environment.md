---
topic: Built Environment
slug: built-environment
---

# Built Environment

## Papers

```dataview
TABLE WITHOUT ID
  year as Year,
  embed(link(file.name + "-thumbnail.png")) as Thumbnail,
  link(file.name, title) as Paper,
  default(venue, "") as Venue
FROM "papers"
WHERE contains(tags, "built-environment")
SORT year DESC
```

## Overview
This topic covers research at the intersection of AI, robotics, and the built environment — including construction automation, structural inspection, site monitoring, progress tracking, and the deployment of intelligent systems in architectural and civil engineering contexts. The built environment presents unique challenges for AI systems: unstructured and continuously evolving physical spaces, strict safety and regulatory requirements, interaction with skilled human workers, and the need for robustness under harsh environmental conditions (dust, weather, vibration). Research spans from perception and planning for construction robots to 3D change detection for construction progress monitoring, embodied inspection agents for infrastructure, and workforce and policy implications of automation in the industry.

## Trends
- **Humanoid robots as general-purpose construction platforms**: Survey evidence suggests humanoid robots may be uniquely suited to construction due to anthropomorphic compatibility with human-designed tools and spaces, bipedal traversal of stairs/scaffolding, and potential to consolidate multiple specialized machines into one versatile platform. However, current platforms face severe payload, battery, and autonomy limitations.
- **Learning from human demonstration for construction tasks**: Rather than hand-coding robot behaviors, recent work enables humanoid robots to learn construction skills directly from video of human workers via pose retargeting and reinforcement learning, bridging the gap between human motion capture and physically executable robot actions.
- **"Long and deep perception" as a construction-specific challenge**: Construction sites demand perception pipelines that both predict how layouts evolve over a workday ("long") and handle robust 3D sensing through dust, occlusion, and clutter ("deep") — requirements that exceed standard indoor or factory perception.
- **From geometric to semantic change detection**: Construction monitoring has evolved from purely geometric change detection (voxel-based, surface distance) toward semantically-aware frameworks that integrate uncertainty quantification and predictive modeling. Recent work defines specialized semantic change types (appeared, disappeared, growth, moved, rotated) enabling nuanced interpretation of construction progress.
- **Predictive scene change modeling**: Scene graph–based approaches are shifting the paradigm from detecting past changes to predicting future changes, enabling proactive construction planning rather than reactive monitoring.
- **Embodied agents for infrastructure inspection**: Virtual embodied agents are being applied to bridge and structural inspection, combining scene graph reasoning with embodied question answering for autonomous assessment of infrastructure condition.

## Open questions
- Can humanoid robots achieve the payload capacity (>50 kg), battery endurance (>4 hours), and environmental robustness needed for practical construction deployment?
- How can humanoid robot controllers move beyond single short-horizon actions to execute long-horizon, multi-step construction workflows (e.g., building a wall) that require sequential task planning?
- How should tool and material interaction be integrated into learning-from-demonstration pipelines, given that current systems only capture posture-based motion without force or grasp modeling?
- What regulatory and liability frameworks are needed for autonomous or semi-autonomous robots operating alongside human workers on active construction sites?
- How should continual learning be implemented for robots that must adapt to new task types across different construction phases without catastrophic forgetting?
- How to rigorously propagate measurement uncertainty, registration error, and segmentation uncertainty through multi-stage 3D change detection pipelines?
- Can uncertainty-aware, semantic-integrated change detection operate in real-time on large construction sites with thousands of objects?
- How to distinguish construction-driven changes (progress) from temporary objects (scaffolding, equipment) for automated progress calculation?
- Do scene graph–based predictions of scene changes transfer across construction types, geographies, and temporal scales?
