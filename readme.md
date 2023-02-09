# Maths

![build](https://github.com/polymonster/maths-rs/actions/workflows/build.yml/badge.svg) [![docs](https://img.shields.io/docsrs/maths-rs/latest)](https://docs.rs/maths-rs/latest/maths_rs/index.html) [![crates](https://img.shields.io/crates/v/maths-rs)](https://crates.io/crates/maths-rs)

Maths is a linear algebra library which aims to be ergonmic and fun to use for gamedev and graphics. If you like writing shaders you should feel right at home. In addition to the usual implementation of vectors, matrices and quaternions it includes a comprehensive collection of utility functions, vector swizzling, intersection tests, point tests, distance functions, trig functions, graphs and signal processing functions.

All features of the crate are enabled by default, you can choose to opt out if you wish.

## Vector

This is a narrow vector library with `Vec2`, `Vec3` and `Vec4` column-vector implementations.  

```rust
/// abbrivated types #[cfg(feature = "short_types")]
pub type Vec2f = Vec2<f32>;
pub type Vec3f = Vec3<f32>;
pub type Vec4f = Vec4<f32>;
pub type Vec4d = Vec4<f64>;
/// ... etc

// construct a vector
let v2 = Vec2f::new(2.0, 3.0);

// the short way #[cfg(feature = "short_hand_constructors")]
let v2_short = vec2f(5.0, 6.0);

// splat a scalar #[cfg(feature = "short_hand_constructors")]
let v3_splat = splat3f(9.0);

// quick common constructors
let y = Vec3f::unit_y();
let b = Vec3f::blue();
let z = Vec3f::zero();
let o = Vec3f::one();
let m = Vec3f::max_value();
// + more

// arithmentic / operators
let result = v2 * v2_short;
let result = v2 + v2_short;
let result = v2 / v2_short;
let result = v2 - v2_short;
let result = -v2;

// construct from tuples and vectors of various sizes
let v4 = Vec4f::from((v2, v2)); // vec4 from 2x v2's
let v3 = Vec3f::from((v2, 1.0)); // vec3 from 1x v2 and 1x scalar
let v2 = Vec2f::from((5.0, 6.0)); // vec2 from 2x scalars
let v4 = Vec4f::from((v2, 0.0, 1.0)); // vec4 from 1x v2 and 2x scalars
let v4 = Vec4f::from(v2); // vec4 from vec2 (splat 0's)
let v2 = Vec2f::from(v4); // vec2 from vec4 (truncate)
// .. and so on

// swizzling
let wxyz = v4.wxyz(); // swizzle
let xyz = v4.xyz(); // truncate
let xxx = v4.xxx(); // and so on..
let xy = v3.yx(); // ..

// mutable swizzles
let mut v = Vec4f::zero();
x.set_xwyz(v); // set swizzle
x.set_xy(v.yx()); // assign truncated
x.set_yzx(v.zzz()); // etc.. 

// type casts #[cfg(feature = "casts")]
let veci = Vec3i::from(vec3f(1.0, 2.0, 3.0));
```

## Matrix

Row-major matrices with `Mat2`, `Mat3`, `Mat34` and `Mat4` implementations.  

```rust
/// abbrivated types #[cfg(feature = "short_types")]
pub type Mat2f = Mat2<f32>;
pub type Mat3f = Mat3<f32>;
pub type Mat34f = Mat34<f32>;
pub type Mat4f = Mat4<f32>; 

// construct from floats
let m4 = Mat4f::new(
    1.0, 0.0, 0.0, 0.0,
    0.0, 1.0, 0.0, 0.0,
    0.0, 0.0, 1.0, 0.0,
    0.0, 0.0, 0.0, 1.0
);

// construct from common matrices
let m34 = Mat34f::from_translation(vec3f(50.0, -10.0, 20.0));
let m4 = Mat4f::from_z_rotation(f32::deg_to_rad(90.0));
let m4 = Mat4f::from_scale(vec3f(10.0, 2.0, 30.0));
let m3 = Mat34f::from_scale(vec3f(10.0, 2.0, 30.0));

// arithmetic
// matrix multiplication
let result = x4 * m4;

// vector transformation
let v4 = vec4f(0.0, 1.0, 0.0, 1.0);
let result = m4 * v4;

// functions
let det = m4.determinant();
let inv = m4.inverse();

// construct rows from tuples
let m3v = Mat3f::from((
    vec3f(1.0, 2.0, 3.0),
    vec3f(4.0, 5.0, 6.0),
    vec3f(7.0, 8.0, 9.0)
));

let m4v = Mat4f::from((
    vec4f(1.0, 2.0, 3.0, 4.0),
    vec4f(5.0, 6.0, 7.0, 8.0),
    vec4f(9.0, 10.0, 11.0, 12.0),
    vec4f(13.0, 14.0, 15.0, 16.0),
));

let m4_rot = Mat4f::from(m3v); // mat4 from mat3
let m4r = Mat3f::from(quat); // from quaternion

// type casts #[cfg(feature = "casts")]
let matd = Mat4d::from(m4v);
```

## Quaternion

Generic floating-point quaternion.

```rust
// abbrivated types #[cfg(feature = "short_types")]
pub type Quatf = Quat<f32>;
pub type Quatd = Quat<f64>;

// construct from euler angles
let q = Quatf::from_euler_angles(x, y, z);

// construct from axis angle
let q2 = Quatf::from_axis_angle(axis, angle);

// arithmetic
let q3 = q * q2;
let q4 = q + q2;
let q5 = q - q2;
let q6 = -q;

// functions
let rev = q.reverse();
let inv = q.inverse();

// type casts #[cfg(feature = "casts")]
let quatd = Quatd::from(q2);
```

## Generic Functions

You can use generic functions on different sized vectors or scalars: `min, max, clamp, step, signum, copysign, abs, deg_to_rad, rad_to_deg, floor, ceil, round, approx, sqrt, powi, powf, sqrt, frac, trunc, modf, rsqrt, recip lerp, nlerp, slerp, smoothstep, dot, perp, cross, mag, mag2, length, distance, dist, dist2, normalize`

```rust
// numeric ops
let int_abs = abs(-1);
let float_abs = abs(-1.0);
let int_max = max(5, 6);
let float_max = max(1.0, 2.0);
let vec_max = max(vec3f(1.0, 1.0, 1.0), vec3f(2.0, 2.0, 2.0));
let vec_min = min(vec4f(8.0, 7.0, 6.0, 5.0), vec4f(-8.0, -7.0, -6.0, -5.0));

// float ops
let fsat = saturate(22.0);
let vsat = saturate(vec3f(22.0, 22.0, 22.0));
let f = floor(5.5);
let vc = ceil(vec3f(5.0, 5.0, 5.0));

// vector ops
let dp = dot(vec2, vec2);
let dp = dot(vec3, vec3);
let cp = cross(vec3, Vec3::unit_y());
let n = normalize(vec3);
let qn = normalize(quat);
let m = mag(vec4);
let d = dist(vec31, vec32);

// interpolation
let fi : f32 = lerp(10.0, 2.0, 0.2);
let vi = lerp(vec2, vec2, 0.5);
let qi = lerp(quat1, quat2, 0.75);
let qs = slerp(quat1, quat2, 0.1);
let vn = nlerp(vec2, vec2, 0.3);
let f = smoothstep(5.0, 1.0, f);
```

## Trigenometry and Logarithmic Functions

These functions are availble for all floating point scalar or vector types: `cos, sin, tan, acos, asin, atan, cosh, sinh, tanh, sin_cos, atan2, exp, exp2, log2, log10`.

They do not have generic implementations as they are not as commonly used, you can access them like so:

```rust
let s = Vec3f::sin(x);
let x = Vec3f::cos(y);
let f = f32::acos(x);
let d = f64::atan2(y, x);
let l = f32::log2(x);
```

### Functions

Plane Classification: `point_vs_plane, aabb_vs_plane, sphere_vs_plane, capsule_vs_plane, cone_vs_plane`.  

Overlaps: `sphere_vs_sphere, sphere_vs_aabb, sphere_vs_obb, aabb_vs_aabb, aabb_vs_frustum, sphere_vs_frustum, sphere_vs_capsule, capsule_vs_capsule, obb_vs_obb, aabb_vs_obb, convex_hull_vs_convex_hull, gjk_2d, gjk_3d`.  

Point Inside: `point_inside_aabb, point_inside_sphere, point_inside_obb, point_inside_triangle, point_inside_cone, point_inside_convex_hull, point_inside_poly, point_inside_frustum`.  

Closest Point: `closest_point_on_aabb, closest_point_on_line, closest_point_on_plane, closest_point_on_obb, closest_point_on_sphere, closest_point_on_ray, closest_point_on_triangle, closest_point_on_polygon, closest_point_on_convex_hull, closest_point_on_cone`.  

Point Distance: `point_aabb_distance, point_segment_distance, point_triangle_distance, distance_on_line, point_plane_distance, plane_distance, point_sphere_distance, point_polygon_distance, point_convex_hull_distance, point_cone_distance, point_obb_distance`.  

Ray / Line: `ray_vs_plane, ray_vs_triangle, ray_vs_sphere, ray_vs_line_segment, ray_vs_aabb, ray_vs_obb, ray_vs_capsule, ray_vs_cylinder, line_vs_line, line_vs_poly, shortest_line_segment_between_lines, shortest_line_segment_between_line_segments`.  

Shader Style Functions: `dot, cross, normalize, mag, mag2, dist, dist2, triple, vector_triple, lerp, nlerp, slerp, saturate, clamp, normalize, all, any, min, max, smoothstep, step, round, floor, ceil, abs, frac, trunc, exp, exp2, log, log2, sin, cos, tan, asin, acos, atan, sinh, cosh, tanh`.  

Graph Functions: `smooth_start, smooth_stop, impulse, cubic_pulse, exp_step, parabola, pcurve, exp_sustained_impulse, sinc, gain, almost_identity, integral_smoothstep, quad_impulse, poly_impulse`.  

\+ More included!
