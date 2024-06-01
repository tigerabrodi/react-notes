# Tailwind got me in the end

I'm officially a Tailwind CSS convert. I've been using it for two months at work, and I'm loving it.

I think it takes time to get around to it, but if your design system in Figma is using Tailwind, it'll make your life so much easier.

# What's this post about?

I wanna dive into the library [cva](https://cva.style/docs) which I've enjoyed so much. It makes building design systems mega fun.

I wanna share how you things would look like without it, and how they do with it.

This will show you a glimpse of how much easier it is to build design systems with `cva`.

We're also gonna be using the [cn](https://tigerabrodi.blog/the-story-behind-tailwinds-cn-function) utility function.

# Let's build a button component without `cva`

We want to have a button component with different variants and sizes:

```tsx
import React, { ComponentProps } from "react";
import { cn } from "@/lib/utils";

type ButtonProps = ComponentProps<"button"> & {
  variant?: "primary" | "secondary" | "danger";
  size?: "small" | "medium" | "large";
};

const Button = ({
  className,
  variant = "primary",
  size = "medium",
  ...props
}: ButtonProps) => {
  const buttonClasses = cn(
    "rounded font-semibold focus:outline-none",
    {
      "bg-blue-500 hover:bg-blue-600 text-white": variant === "primary",
      "bg-gray-200 hover:bg-gray-300 text-gray-800": variant === "secondary",
      "bg-red-500 hover:bg-red-600 text-white": variant === "danger",
      "px-4 py-2 text-sm": size === "small",
      "px-6 py-3 text-base": size === "medium",
      "px-8 py-4 text-lg": size === "large",
    },
    className
  );

  return <button className={buttonClasses} {...props} />;
};

export default Button;
```

I'm excluding `forwardRef` here. But if you need that, you'd have to add it. Although, it's gonna go away with React 19.

The pain of building a component this way is all the conditional classes. The code becomes bloated and hard to read.

It would be nice to have a tiny thing responsible for abstracting this complexity.

# `cva` to the rescue

```tsx
import React from "react";
import { cva, VariantProps } from "class-variance-authority";
import { cn } from "@/lib/utils";

const buttonVariants = cva("rounded font-semibold focus:outline-none", {
  variants: {
    variant: {
      primary: "bg-blue-500 hover:bg-blue-600 text-white",
      secondary: "bg-gray-200 hover:bg-gray-300 text-gray-800",
      danger: "bg-red-500 hover:bg-red-600 text-white",
    },
    size: {
      small: "px-4 py-2 text-sm",
      medium: "px-6 py-3 text-base",
      large: "px-8 py-4 text-lg",
    },
  },
  defaultVariants: {
    variant: "primary",
    size: "medium",
  },
});

type ButtonProps = VariantProps<typeof buttonVariants> &
  React.ButtonHTMLAttributes<HTMLButtonElement>;

const Button: React.FC<ButtonProps> = ({
  className,
  variant,
  size,
  ...props
}) => {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  );
};

export default Button;
```

`cva` let's you define your variants and sizes in a single place. It abstracts the complexity of conditional classes.

The nice part here is that you don't need to worry about the typings. `VariantProps<typeof buttonVariants>` will take care of that for you. You can focus on the complexty of the designs in a different place. This place being the `cva` function.

It returns a function that you can call with the props you want to apply. It then returns the Tailwind classes you need.

## Compound Variants

If you need styles to apply when multiple variants are met, you can do that with [Compound Variants](https://cva.style/docs/getting-started/variants#compound-variants).

# The confusion around `variants`

I'll be honest, `variants` is a bit confusing because you typically have a `variant` prop. But in the case of `cva`, `variants` are the props. Which would honestly be a better name for it.
