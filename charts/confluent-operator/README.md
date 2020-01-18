Note: in order to make this work with GitOps and FLux, you need to flatten out this helm chat into one each per component.

This is because this is a priviate heml chart and not published, so sub charts are internal. This breaks typical CD at times with helm.
