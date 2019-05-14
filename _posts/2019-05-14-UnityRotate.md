---
layout: post
title: Unity Rotate
---

# Vector Rotate
```markdown
  Vector3 direction = 값;
  direction = Quaternion.Euler(0.0f, 0.0f, -deltaAngle) * direction;
```

```markdown
    Vector2 toTarget = (target.position - transform.position).normalized;
    float step = Time.deltaTime * rotateAngleSpeed * Mathf.Deg2Rad;
    rotateVector = Vector3.RotateTowards(rotateVector, toTarget, step, 0.0f);
```

# Quaterinon Rotate
```markdown
  Vector3 direction = 값;
  direction = Quaternion.Euler(0.0f, 0.0f, -deltaAngle) * direction;
```

```markdown
 rotateChild.rotation = Quaternion.RotateTowards(rotateChild.rotation, aa, Time.deltaTime * angleSpeed);
```

```markdown
 Vector2 toTargetDir = (Vector2)targetPosition - (Vector2)transform.position;
 var quaternionToTarget = Quaternion.FromToRotation(transform.right, toTargetDir) * transform.rotation;
 transform.rotation = Quaternion.RotateTowards(transform.rotation, quaternionToTarget, Time.deltaTime * m_AimToTargetAngleSpeed);
```

# GetNegativeAngle
``` markdown
public static float GetNegativeAngle(Vector3 v1, Vector3 v2)
{
  int sign = Vector3.Cross(v1, v2).z < 0 ? -1 : 1;
  return sign * Vector3.Angle(v1, v2);
}
```
