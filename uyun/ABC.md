##### 检验

1.注解

```java
@Target({ElementType.METHOD, ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Constraint(validatedBy = ScheduleValidator.class)
public @interface ValidSchedule {

    boolean notNull() default false;

    String message() default "invalid schedule";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}

```

2.参数校验

```java
public class ScheduleValidator implements ConstraintValidator<ValidSchedule, Object> {
    private static Logger logger = LoggerFactory.getLogger(ScheduleValidator.class);
    private boolean flag;
    private ValidSchedule validSchedule;

    @Override
    public void initialize(ValidSchedule validSchedule) {
        this.validSchedule = validSchedule;
    }

    @Override
    public boolean isValid(Object o, ConstraintValidatorContext constraintValidatorContext) {
        flag = false;
        if (o == null) {
            return !validSchedule.notNull();
        }
        /**
         * 同一倒班时间内值班人员不能重复
         */
        if (o instanceof ScheduleVO) {
				......
        return flag;
    }
}
```

