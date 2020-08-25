# Next.js

#
#nextjs #react #navigation #layout #authentication
#

## Navigation
### Layout
- Create a `Layout.js` file

    ```
    const Layout = props => (
        <div>
            <NavBar />
            <div>
                {props.children}
            </div>
        </div>
    );
    ```

- Use in `pages` like:
    ```
    const Index = () => <Layout>Welcome!</Layout>;
    ```

#
### Nav

- Define `navButtons.js`
    ```
    const navButtons = [
        { label: "home", path: "/" },
        { label: "about", path: "/about" }
    ];
    ```

- Define `NavButton.js`

    <div class="note">
        <b>Router</b> keeps track of <b>active page</b> through <code>router.pathname</code> property. 
        <br/><br/>
        To inject the <b>pathname</b> prop use the <code>withRouter</code> <b>higher-order</b> component.
        <br/><br/>
        Using this prop with conditional styling for a <b>distinctive style for active page button</b>
    </div>
    <br/>

    ```
    import Link from "next/link";
    import { withRouter } from "next/router";

    const NavButton = props => (
        <Link href={props.path}>
            <div
                className={`NavButton ${
                    props.router.pathname === props.path ? "active" : ""
            }`}
            >
                <div className="Icon">{props.icon}</div>
                <span className="Label">{props.label}</span>
            </div>
        </Link>
    );

    export default withRouter(NavButton);
    ```

- Define `NavBar.js`

    ```
    const NavBar = props => (
        <div>
            {props.navButtons.map(button => (
                <NavButton
                    key={button.path}
                    path={button.path}
                    label={button.label}
                />
            ))}
        </div>
    );
    ```

That's it for **Nav!**

#
## Auth
Use the `next-auth` package. (Built in Auth)