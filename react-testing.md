# React Testing

## Base Test Case

```typescript
import React from "react";
import { render, screen } from "@testing-library/react";
import MyComponent from "./MyComponent";

describe("MyComponent", () => {
  it("renders without crashing", () => {
    render(<MyComponent />);
    expect(screen.getByRole("button")).toBeInTheDocument();
  });

  it("displays the correct text", () => {
    render(<MyComponent />);
    expect(screen.getByText("Click me!")).toBeInTheDocument();
  });

  it("handles click events", () => {
    const handleClick = jest.fn();
    render(<MyComponent onClick={handleClick} />);
    const button = screen.getByRole("button");
    button.click();
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

## verifying the correct styles

```typescript
import React from "react";
import { render, screen } from "@testing-library/react";
import MyComponent from "./MyComponent";

describe("MyComponent", () => {
  it("applies the correct styles to the button", () => {
    render(<MyComponent />);
    const button = screen.getByRole("button");
    expect(button).toHaveStyle({
      backgroundColor: "blue",
      color: "white",
      padding: "10px",
      borderRadius: "5px",
    });
  });
});
```

## verifying correct styles for layout depending on isMobile

```typescript
import React from "react";
import { render, screen, act } from "@testing-library/react";
import MyComponent from "./MyComponent";

describe("MyComponent", () => {
  it("applies the correct styles to the button for isMobile viewport width", () => {
    act(() => {
      Object.defineProperty(window, "innerWidth", {
        writable: true,
        configurable: true,
        value: 480, // Set the viewport width to 480 pixels
      });
    });

    render(<MyComponent />);
    const button = screen.getByRole("button");
    expect(button).toHaveStyle({
      backgroundColor: "red",
      color: "white",
      padding: "5px",
      borderRadius: "3px",
    });
  });
});
```

## verifying api calls from function actions

```typescript
import React from "react";
import { render, fireEvent } from "@testing-library/react";
import MyComponent from "./MyComponent";
import { fetchData } from "./api-services";

jest.mock("./api-services", () => ({
  fetchData: jest.fn(),
}));

describe("MyComponent", () => {
  it("calls fetchData with the correct payload when the button is clicked", () => {
    const payload = { id: 123 };
    render(<MyComponent payload={payload} />);
    const button = screen.getByRole("button");
    fireEvent.click(button);
    expect(fetchData).toHaveBeenCalledWith(payload);
  });
});
```

## verifying ui interaction between components for backend call

```typescript
import React from "react";
import { render, fireEvent } from "@testing-library/react";
import MyComponent from "./MyComponent";
import { fetchData } from "./api-services";

jest.mock("./api-services", () => ({
  fetchData: jest.fn(),
}));

describe("MyComponent", () => {
  it("calls fetchData with the correct amount when the slider is changed and the button is pressed", () => {
    const initialAmount = 10;
    render(<MyComponent initialAmount={initialAmount} />);
    const slider = screen.getByRole("slider");
    const button = screen.getByRole("button");
    const newAmount = 20;

    // Simulate slider change
    fireEvent.change(slider, { target: { value: newAmount } });

    // Simulate button click
    fireEvent.click(button);

    // Check that the API was called with the correct amount
    expect(fetchData).toHaveBeenCalledWith(newAmount);
  });
});
```

## INCORRECT - order of tests creates different results

```typescript
describe("MyComponent", () => {
  it("applies the desktop button styles correctly", () => {
    act(() => {
      Object.defineProperty(window, "innerWidth", {
        writable: true,
        configurable: true,
        value: 1024, // Set the viewport width to 1024 pixels (desktop view)
      });
    });

    render(<MyComponent />);
    const button = screen.getByRole("button");
    expect(button).toHaveStyle({
      backgroundColor: "blue",
      color: "white",
      padding: "10px",
      borderRadius: "5px",
    });
  });

  it("applies the mobile button styles correctly", () => {
    act(() => {
      Object.defineProperty(window, "innerWidth", {
        writable: true,
        configurable: true,
        value: 480, // Set the viewport width to 480 pixels (mobile view)
      });
    });

    render(<MyComponent />);
    const button = screen.getByRole("button");
    expect(button).toHaveStyle({
      backgroundColor: "red",
      color: "white",
      padding: "5px",
      borderRadius: "3px",
    });
  });
});
```

## CORRECT - tests are independent of each other

using beforeEach and afterEach we can reset the state before each test to ensure that previous state does not affect current test conditions.

```typescript
describe("MyComponent", () => {
  let originalInnerWidth: number;

  beforeEach(() => {
    originalInnerWidth = window.innerWidth;
  });

  afterEach(() => {
    Object.defineProperty(window, "innerWidth", {
      writable: true,
      configurable: true,
      value: originalInnerWidth,
    });
  });

  it("applies the desktop button styles correctly", () => {
    Object.defineProperty(window, "innerWidth", {
      writable: true,
      configurable: true,
      value: 1024, // Set the viewport width to 1024 pixels (desktop view)
    });

    render(<MyComponent />);
    const button = screen.getByRole("button");
    expect(button).toHaveStyle({
      backgroundColor: "blue",
      color: "white",
      padding: "10px",
      borderRadius: "5px",
    });
  });

  it("applies the mobile button styles correctly", () => {
    Object.defineProperty(window, "innerWidth", {
      writable: true,
      configurable: true,
      value: 480, // Set the viewport width to 480 pixels (mobile view)
    });

    render(<MyComponent />);
    const button = screen.getByRole("button");
    expect(button).toHaveStyle({
      backgroundColor: "red",
      color: "white",
      padding: "5px",
      borderRadius: "3px",
    });
  });
});
```

##

```typescript

```

##

```typescript

```

##

```typescript

```

##

```typescript

```
