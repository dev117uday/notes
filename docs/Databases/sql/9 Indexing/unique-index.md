# Unique Index

```sql
CREATE UNIQUE INDEX idx_products_product_id
    ON public.products USING btree
    (product_id ASC NULLS LAST)
    TABLESPACE pg_default;


CREATE UNIQUE INDEX idx_u_emp_emp_id
    ON public.employees USING btree
    (employee_id ASC NULLS LAST)
    TABLESPACE pg_default;

CREATE UNIQUE INDEX idx_orders_customer_id_order_id
    ON public.orders USING btree
    (order_id ASC NULLS LAST, customer_id ASC NULLS LAST)
    TABLESPACE pg_default;


CREATE UNIQUE INDEX idx_u_emp_emp_id_hire_date
    ON public.employees USING btree
    (employee_id ASC NULLS LAST, hire_date ASC NULLS LAST)
```

